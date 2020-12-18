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

在安裝 Docker 時，會建立一個新的網卡，名字就叫做 `docker0`，在這之後每一個 Docker 容器都會在這一個網卡上分配一個 IP 位置，我們可以使用 `ip a show docker0` 來看看：

![1](imgs/1.png)