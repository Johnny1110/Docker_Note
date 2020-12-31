# 環境變量

<br>

---

<br>

這邊的章節會介紹如何在 Docker Compose 中對容器套用環境變量。

<br>

## 在容器中設定環境變數

<br>

```yml
web:
  environment:
    - DEBUG=1
```

<br>
<br>
<br>
<br>

## 用 ".env" 文件設定環境變數

<br>

我們可以使用 `env_file` 指令來把 .env 文件的環境變數配置到容器內。

<br>

web-variables.env 文件：

```
JRE_HOME=/usr/java/jre_1.8/
```

<br>

docker-compose.yml 文件：

```yml
web:
  env_file:
    - web-variables.env
```

<br>
<br>
<br>
<br>

## “.env” 文件

我們可以在同 docker-compose.yml 的工作目錄下，建立一個 .env 文件，設定一些環境變數，並且可以在 docker-compose.yml 文件中就可以直接使用：

<br>

.env 文件：

```
TAG=v1.5
```

<br>

docker-compose.yml 文件：

```yml
version: '3'
services:
  web:
    image: "webapp:${TAG}"
```
