# Docker 鏡像

<br>

---

<br>

## 鏡像介紹

<br>

關於鏡像結構，看下面這一張圖會就夠了。

![1](imgs/1.png)

<br>

說實話，第一次看到這張圖配上各種版本的解說還是完全沒概念。但是真正玩過一段時間 docker 之後，會發現 docker 鏡像真的就跟這張圖一樣簡單明瞭。如果是新學 docker 的話，建議這邊先看過就好，不理解沒關係，之後重新回來看一遍這張圖就好。

Docker 是由文件系統疊加而成，最底端視引導文件系統（bootfs）。基本上我們作為 docker 的使用者是不會跟引導文件系統有任何關係的。

第二層是 root 文件系統（rootfs），位於引導文件系統之上。他可以是多種 OS （CentOS丶RedHat丶Ubuntu）

再來第三層開始就是我們需要的應用程式了。一個鏡像可以放到另一個鏡像的頂部。

<br>
<br>

## 列出鏡像

<br>

想要知道目前主機上有哪些 Docker 鏡像，可以使用 docker images 實現：

```bash
sudo docker images
```

<br>

Docker Hub 上有兩種類型的倉庫，一種是用戶倉庫 （user repository）另一種是頂層倉庫 （top-level repository），用戶倉庫是給像我們這樣的一般使用者上傳自己製作的鏡像用的，頂層倉庫是由 Docker 公司內部的開發人員管理。

基本上用戶倉庫跟頂層倉庫很好區分，用戶倉庫的命名由2個部份組成 ：

* 用戶名稱：johnny1110

* 倉庫名稱：demo_container

組合起來會像這樣 `johnny1110/demo_container`。頂層倉庫則沒有用戶名稱，只有倉庫名稱這個部份：`demo_comtainer` 像這樣。

<br>

---
### tips

* 所有本地端鏡像都保存在 `/var/lib/docker` 目錄下。

* 所有容器都飽存在 `/var/lib/docker/containers` 內。
---

<br>
<br>

## 拉取鏡像

<br>

我們可以從 Docker Registry 上拉取需要的鏡像到本地端，例如我們可以從 Docker Hub 上拉取其他本的 Ubuntu。

```bash
sudo docker pull ubuntu:12.04
```
<br>

為了區分同一個倉庫的不同鏡像，Docker 提供了 tag 功能。每個鏡像都會有一個標籤，舉 Ubuntu 為例，就會有 latest丶12.04 等等。

<br>
<br>

## 查找鏡像

<br>

查找鏡像可以在 Docker Hub 進行，也可以通過 `docker search` 查詢。

`sudo docker search ubuntu`


