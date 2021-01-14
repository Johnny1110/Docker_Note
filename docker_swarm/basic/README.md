# docker-swarm 基礎操作

<br>

---

<br>
<br>

## 前置作業

<br>

docker-swarm 的實作練習需要有2台或以上的主機，使用虛擬機也可以，這裡我使用 GCP 來示範。沒有2台主機或者不能用虛擬機的可以考慮註冊 GCP，可以免費用半年。

<br>

之後關於 docker-swarm 的介紹我都會以 GCP 作為範例演示，使用虛擬機也不必擔心，都大同小異。

先開好 2 台機器，OS 盡量使用 Ubuntu 20.04：

![1](imgs/1.jpg)

如果你是用虛擬機的，同樣也能查到主機 ip。以下針對 ip 的操作就請替換成你自己的主機 ip。

<br>

在兩台虛擬機中分別安裝 docker，請確保版本一致，如果你是用 Ubuntu 20.04 的話執行下列命令就可以快速安裝了:

```bash
sudo apt-get update
sudo apt-get install docker docker.io
```

<br>

安裝好後檢查一下版本：

![2](imgs/2.jpg)

<br>

都沒問題的話，我們就可以正式開始時做 docker-swarm 了。

<br>
<br>
<br>
<br>

## 建立群集

<br>

我們選定 vm1 當作 Manager，vm2 當作 worker。所以我們先針對 vm1 做一些設定：

<br>

```bash
sudo docker swarm init --advertise-addr 10.140.0.2
```

我們初始化設定了 vm1 為 leader 同時為 manager（記得這邊 ip 換你自己的 vm1 子網 ip）

<br>

如果不出意外的話應該會看到這個畫面：

<br>

![3](imgs/3.jpg)

<br>

這裡一共給我們 2 個資訊，一是現在這個 node （vm1）已經是 swarm 的管理者了，第二個是如果要把其他 node 加入到這個 swarm 中作為 worker 只要輸這個指令：

<br>

```bash
docker swarm join --token SWMTKN-1-3hzht65p6a1nhl2oj9qn2yel1899xkzdzxcw4xu4847wchju5e-6wd5ppdiegwgp8jrm7q0or1h5 10.140.0.2:2377
```

<br>

這個指令不用抄，如果忘記了就叫 docker 再顯示一次就好：

<br>

```bash
sudo docker swarm join-token worker
```

<br>

![4](imgs/4.jpg)

<br>

如果想把其他 node 以 manager 的身分加入到 swarm 稍微改一下查詢指令就可以了：

<br>

```bash
sudo docker swarm join-token manager
```

<br>

現在就來把 vm2 加入到 swarm 中吧，切到 vm2 的 shell 介面，直接貼上剛剛的 swarm join 指令：

![5](imgs/5.jpg)

再次提醒要注意我們是要在 vm2 做喔!!!現在已經成功把 vm2 加入到 swarm 了。

<br>

現在讓我們回到 vm1 去確認一下目前 swarm 狀態。

<br>

```bash
sudo docker ndoe ls
```

<br>

![6](imgs/6.jpg)

<br>

這裡不多做解釋了，應該都看得懂意思。如果你在 vm2 上執行這個指令會失敗，因為針對 swarm 做任何操作只有 manager 才有權進行，vm2 只是 work，所以它只要負責做事就好了，並不需要知道 swarm 名單以及對 swarm 進行操作。

<br>

如果要把 vm2 從 swarm 中移除，必須要讓 vm2 的 status 處於 down 得狀態，怎麼做呢 ? 可以直接把 vm2 關機，或者 vm2 主動離開 swarm：

<br>


在 vm2 中執行
```bash
sudo docker swarm leave
```

無論用哪一個方法，我們回到 vm1 再次查看都會發現 vm2 已經處於 down 的狀態了（ps 可能要等一下才會看到）：

<br>

![7](imgs/7.jpg)

現在就可以刪除它了：

```bash
sudo docker node rm vm2
```

<br>

如果今天要刪除的是一個 manager node，我們也不能直接把它刪除，正確作法是先將它 demote 成 worker，然後再用刪除 worker 的方法把它刪除。

<br>

假如 vm3 是一個 manager，我們要先 demote 它：

<br>

```bash
sudo docker node demote vm3
```

<br>

然後再刪除：

```bash
sudo docker node rm vm3
```

<br>

或許這個時候你會想 `rm` 一下 vm1 試試看，它會告訴你不可以移除最後一個 manager，確實，我們只指定了一個 manager，解決方法就是直接 `docker swarm leave` 就好了，記得要加 `--force` :

<br>

```bash
sudo docker swarm leave --force
```

<br>
