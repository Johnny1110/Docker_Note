# Docker 容器間溝通

<br>
<br>

---

<br>
<br>

docker 的容器與容器之間要想進行互動溝通就必須要用以下 3 種方法：

* Docker 內部網路（ _不推薦_ ）

* Docker Networking（ _推薦_ ）

* Docker 鍊接

<br>

Docker 內部網路並不靈活好用，Docker 鍊接也已經是舊版的產物了，但是為了介紹 Docker 網路的運作概念，這邊三個方法都會介紹，但是實際使用上推薦使用 Docker Networking。

<br>
<br>

---

<br>
<br>

## Docker 內部網路

<br>

在安裝 Docker 時，會建立一個新的網卡，名字就叫做 `docker0`，在這之後每一個 Docker 容器都會在這一個網卡上分配一個 IP 位置，我們可以使用 `ip a show docker0` 來看看：

<br>

![1](imgs/1.png)

<br>

網卡 docker0 用於連接容器與主機網路，如果我們用 `ip a` 看一下，會發現一些以 veth 開頭的網卡如下：

<br>

![2](imgs/2.png)

<br>

Docker 每創建一個容器就會建立一組網卡，這組網卡其中一端作為容器內的 eth0 接口，另一端就會是主機上命名類似 veth... 這種接口。而主機上的 veth 接口都是隸屬於 docker0 網卡。可以理解為 veth 接口插在 docker0 這個 bridge 上，另一端插在容器內。

<br>

![3](imgs/3.jpg)

<br>

透過把每一個 veth* 街口綁定到 docker0 網卡，Docker 建立了一個虛擬子網路。這個子網路由主機與所有 Docker 容器共享。

<br>

這邊有一個例子，我預先建立好了一個容器，這個容器內運行著 nginx 服務：

![3](imgs/3.png)

<br>

接下來我們使用 `docker exec` 進入到到容器內部並查看其 IP 位址：

<br>

![4](imgs/4.png)

<br>

這裡可以看到容器分配到的 IP 位址為 172.17.0.2，前面使用 `docker ps` 指令看到容器內 80 port 映射主機 8080 port，所以這也意味著我們可以直接在主機上訪問 172.17.0.2:80 來訪問容器的 nginx 服務。

<br>

![5](imgs/5.png)

<br>

這種方式似乎可以用來作屎容器間互通的方案，但是缺點很明顯，為了要使容器間互通，我們必須要在容器內作硬編碼，舉例說就是 A B 兩ㄖ器內的應用要互通，那就要在 A 容器內設定 B 容器的 IP 位址與 port，B 容器也要如此。還有一點更為致命，就是 Docker 容器一旦重啟，IP 位址就會改變，這樣一來又要重新進入容器設定。這顯然不是一個好方法。

<br>
<br>
<br>
<br>

## __Docker Networking__ （很重要）


<br>

Docker 1.9 版本之後發布了 Docker Networking 功能，它允許使用者們建立自己的網路，容器們可以透過這個自訂網路建立連接。更加厲害的是，現在 Docker 容器甚至可以跨越不同主機來建立連接了。

<br>

這一個 part 要注意的東西有很多，稍微有點難度，看不懂就多看幾遍吧。

<br>
<br>

### 實現目標

<br>

首先先介紹一下在這一個章節我們要做什麼，我們要使用 docker 將 2 個鏡像容器化，並可以互相溝通。一個是 redis 官方鏡像，另一個是我自己用 java 寫的一個 web 服務鏡像，用於向 redis 寫入與取出資料的。

<br>

關於我寫的容器有以下幾點要說明：

<br>

* web 服務綁定到容器 5000 port。

<br>

* __存入 record 的 api__: 

    * url: /redis/set
    * method: GET
    * params:
        * key
        * value
    * return: success

<br>

* __取出 record 的 api__: 
    * url: /redis/get
        * method: GET
        * params:
            * key
        * return: value


<br>
<br>


當服務架設好後，應該要能夠：

* 開啟瀏覽器輸入 `http://localhost:5000` 就會看到歡迎頁面。

* 輸入 `http://localhost:5000/redis/set?key=test&value=123` 就可以像 redis 插入一條 record。（key=test, value=123），並回傳 success 資訊。

* 輸入 `http://localhost:5000/redis/get?key=test` 回回傳 redis 中 key=test 的資料。



<br>
<br>
<br>
<br>

### 開始實做

<br>

首先關於鏡像的部份可以先提前 pull 下來：

```bash
sudo docker pull redis
```

```
sudo docker pull johnny1110/rediswebapp
```

<br>

接下來我們來建立一個自訂的 docker network，指令如下：

```bash
sudo docker network create app
```

<br>

現在我們已經建立好一個名為 app 的 network 了，讓我來看看這個 network 裡面的細節資訊吧：

```bash
sudo docker network inspect app
```

<br>

![6](imgs/6.png)

<br>

這是一個新的本地橋接（bridge）網路，而且沒有任何容器正在網路內運行。

<br>

可以使用 `docker network ls` 來查詢 docker 所有的 network。

<br>
<br>


