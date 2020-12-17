# 刪除鏡像 `rmi`

<br>

---

<br>

刪除鏡像指令：

```bash
sudo docker rmi <鏡像名稱/鏡像ID>
```

<br>

有些時候鏡像會也依賴關係，被子類依賴的父類沒辦法直接刪除，這時我們可以加上 `-f` 參數強行刪除：

```bash
sudo docker rmi -f <鏡像名稱/鏡像ID>
```

<br>

鏡像也可以一次刪除多筆：

```bash
sudo docker rmi <鏡像名稱/鏡像ID> <鏡像名稱/鏡像ID> <鏡像名稱/鏡像ID> ...
```

<br>

若想一次刪除全部鏡像，作法跟先前提到的一次刪除全部容器作法一樣：

```bash
sudo docker rmi `sudo docker images -q -a`
```