# 拉取鏡像

<br>

---

<br>

我們可以從 Docker Registry 上拉取需要的鏡像到本地端，例如我們可以從 Docker Hub 上拉取其他本的 Ubuntu。

<br>


```bash
sudo docker pull ubuntu:12.04
```

<br>

為了區分同一個倉庫的不同鏡像，Docker 提供了 tag 功能。每個鏡像都會有一個標籤，舉 Ubuntu 為例，就會有 latest丶12.04 等等。