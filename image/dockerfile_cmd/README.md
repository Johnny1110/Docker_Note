# Dockerfile 指令

<br>

---

<br>

這里會詳細列舉出 Dockerfile 的指令，可以把這邊當作是 Dockerfile 指令字典，需要時可以回來看一看。

<br>

## 目錄

<br>

* [`CMD`](#1)

* [`ENTRYPOINT`](#2)

* [`WORKDIR`](#3)

* [`ENV`](#4)

* [`USER`](#5)

* [`VOLUME`](#6)

* [`ADD`](#7)

* [`COPY`](#8)

* [`LABEL`](#9)

* [`STOPSIGNAL`](#10)

* [`ARG`](#11)

* [`ONBUILD`](#12)

<br>
<br>

---

<br>
<br>

<div id="1">

## `CMD`

<br>

`CMD` 的作用跟使用 `docker run` 命令啟動容器時指定命令類似。例如：

```bash
sudo docker run -it johnny1110/static_web /bin/bash
```

可以在 Dockerfile 這樣設定：

```dockerfile
CMD ["/bin/bash"]
```

如果要傳遞參數可以這樣做（多說一句，這跟　java ProcessBuilder 處理方式很像）：

```dockerfile
CMD ["/bin/bash", "-l"]
```

__使用 `docker run` 指令可以覆蓋 `CMD` 指令__，如果 Dockerfile 有 `CMD`，`docker run` 時也指定命令，此時會以後者為主。

Dockerfile 中只能指定一條 `CMD` 指令，如果指定了多條，會以最後一條為主。

<br>
<br>

---

<br>
<br>

<div id="2">

## `ENTRYPOINT`

<br>

`ENTRYPOINT` 跟 `CMD` 也很像，但又不一樣。使用方式如下：

在 Dockerfile 中編寫以下命令：

```dockerfile
ENTRYPOINT ["/usr/sbin/nginx"]
```

建構好鏡像後，使用 `docker run` 啟動容器方法如下：

```bash
sudo docker run -it johnny1110/static_web -g "daemon off;"
```

這次我們就不必去指定 nginx 了，只要把 nginx 啟動需要的參數傳入即可。

<br>
<br>

也可以把 `ENTRYPOINT` 當作 `CMD` 來用

```dockerfile
ENTRYPOINT ["/usr/sbin/nginx", "-g", "daemon off;"]
```

這樣一來啟動器時連參數都不用再給了。

<br>
<br>

`ENTRYPOINT` 與 `CMD` 可以並用，例如：

```dockerfile
ENTRYPOINT ["/usr/sbin/nginx"]
CMD ["-h"]
```

當我們這樣設定時，使用 `docker run` 時如果不傳入參數，預設就會是 `-h`（help），如果 `docker run` 指定了參數當然就用我們要傳入的參數了。


<br>
<br>

---

<br>
<br>

<div id="3">

## `WORKDIR`

<br>

`WORKDIR` 用來指定建構鏡像時的工作目錄（就是你 `cd` 到哪裡）。例如：

```dockerfile
WORKDIR /tmp
RUN touch test.txt
WORKDIR /opt
RUN touch test2.txt
```

應該看就懂什麼意思。其實就是 `cd` 指令。

<br>

`docker run` 時也可以用 `-w` 來指定 WORKDIR，同時也可以覆蓋運行時的工作目錄：

```bash
sudo docker run -it -w /var/log ubuntu pwd
```

<br>
<br>

---

<br>
<br>

<div id="4">

## `ENV`

<br>
<br>

---

<br>
<br>

<div id="5">

## `USER`

<br>
<br>

---

<br>
<br>

<div id="6">

## `VOLUME`

<br>
<br>

---

<br>
<br>

<div id="7">

## `ADD`

<br>
<br>

---

<br>
<br>

<div id="8">

## `COPY`

<br>
<br>

---

<br>
<br>

<div id="9">

## `LABEL`

<br>
<br>

---

<br>
<br>

<div id="10">

## `STOPSIGNAL`

<br>
<br>

---

<br>
<br>

<div id="11">

## `ARG`

<br>
<br>

---

<br>
<br>

<div id="12">

## `ONBUILD`

<br>
<br>