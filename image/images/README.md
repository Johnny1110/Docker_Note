# 列出鏡像

<br>

---

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