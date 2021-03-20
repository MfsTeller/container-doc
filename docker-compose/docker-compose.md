## Docker Compose

### Reference

* [Docker Compose](https://docs.docker.jp/compose/toc.html)

### Steps to Using Docker Compose

. Dockerfileを作成する
. docker-compose.yml にアプリケーションを構成するサービスを定義する
. `docker-compose up` を実行する

### docker-compose.yml

* Docker Composeは `docker-compose.yml` を基にサービスを構築する
* 以下はpostgresイメージを利用する例である

~~~
version: '3'
services:
  postgres:
    image: postgres:13.2-alpine
    container_name: postgres
    environment:
      POSTGRES_USER: root
      POSTGRES_PASSWORD: admin
      POSTGRES_INITDB_ARGS: "--encoding=UTF-8 --locale=ja_JP.UTF-8"
    ports:
      - 5432:5432
    user: root
    tty: true
    volumes:
      - ./initdb:/docker-entrypoint-initdb.d
~~~

### Start Docker-Compose Service

* Docker Composeサービスは `docker-compose up` コマンドを利用して起動する

~~~
# background execution
$ docker-compose up -d
~~~

### Stop Docker-Compose Service

* Docker Composeサービスは `docker-compose stop` コマンドを利用して停止する

~~~
$ docker-compose stop
~~~

* サービスを停止・削除するには `docker-compose down` コマンドを利用する

~~~
$ docker-compose down --volumes
~~~

### Restart Docker-Compose Service

* Docker Composeサービスは `docker-compose up` コマンドを利用して再起動する

~~~
$ docker-compose up -d
~~~

### List Docker-Compose Service

* 起動中のDocker Composeサービスは `docker-compose ps` コマンドを利用して表示する

~~~
$ docker-compose ps
~~~

### Execute Command in a Running Docker-Compose Service

* 起動中のDocker Composeサービスでコマンドを実行するには `docker-compose run` コマンドを利用する

~~~
$ docker-compose run <Service> <Command>
[Example]
$ docker-compose run postgres env
~~~