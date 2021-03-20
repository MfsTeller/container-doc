## Docker

### Reference

* [青山真也 (2020) Kubernetes 完全ガイド, 株式会社インプレス](https://book.impress.co.jp/books/1119101148)
* [Dockerfileのベストプラクティス](https://docs.docker.jp/develop/develop-images/dockerfile_best-practices.html)

### Docker Container Design

* Dockerコンテナを生成する際は下記に留意する
  * 1コンテナにつき1プロセスにする
  * Immutable Infrastructureなイメージにする
    * 環境を変更する際は、古い環境を破棄して新たに作成する
    * 一度作られた環境は変更されないことを徹底する
  * 軽量なDockerイメージにする
  * 実行ユーザーをroot以外にする

### Dockerfile

* DockerイメージはDockerfileを元に生成する

~~~
FROM golang:1.16.2-alpine

COPY ./pkg/main.go ./

RUN go build ./main.go -o app

USER nobody

ENTRYPOINT ["./app"]
~~~

### Build Docker Image

* Dockerイメージは `docker build image` コマンドを利用して生成する

~~~
# move to a directory
$ cd /home/user/docker-practice

# create docker image
$ docker image build -t practice-image:0.1 ./

# check docker image
$ docker images 
~~~

### Multi-Stage Build

* 上記のGolangのDockerイメージはGolangのコンパイラツール等も含むためサイズが大きい
* この問題を解決するため、マルチステージビルドを利用する

~~~
# STAGE 1
FROM golang:1.16.2-alpine as builder
COPY ./pkg/main.go ./
RUN go build ./main.go -o /app

# STAGE 2
FROM alpine:3.13
COPY --from=builder /app ./
ENTRYPOINT ["./app"]
~~~

### Remove Docker Image

* Dockerイメージは `docker rmi` コマンドを利用して削除する

~~~
$ docker rmi practice-image:0.1
~~~

* 利用していない全てのDockerイメージを削除するには、 `docker image prune` コマンドを利用する

~~~
$ docker image prune
~~~

### Push Docker Image to Docker Registry

* DockerレジストリはDockerイメージを保存するリポジトリサーバーである
  * e.g. Docker-Hub, Azure Container Registry (ACR)
* Docker-Hubには数多くのDockerイメージが公開されているが、公式イメージ(OFFICIAL REPOSITORY)の利用を推奨する
* DockerレジストリにDockerイメージをプッシュするためには、イメージ名を下記のフォーマットにする

~~~
<Docker Registry Host>/<Namespace>/<Repository>:<Tag>
[Example]
myregistry.azurecr.io/samples/practice-image:0.1
~~~

* `docker login` コマンドを利用してDockerレジストリにログインする

~~~
$ docker login myregistry.azurecr.io
~~~

* `docker image tag` コマンドを利用してタグを付け替える

~~~
$ docker image tag practice-image:0.1 myregistry.azurecr.io/samples/practice-image:0.1
~~~

* Dockerイメージは `docker push` コマンドを利用してDockerレジストリにプッシュする

~~~
$ docker image push myregistry.azurecr.io/samples/practice-image:0.1
~~~

* `docker logout` コマンドを利用してDockerレジストリからログアウトする

~~~
$ docker logout
~~~

### Start Docker Container

* Dockerコンテナは `docker run` コマンドを利用して起動する

~~~
# apply background running option
$ docker run --name practice-app -d practice-image:0.1
~~~

### Restart Docker Container

* Dockerコンテナは `docker restart` コマンドを利用して再起動する

~~~
$ docker restart practice-app
~~~

### Login Docker Container

* Dockerコンテナへは `docker exec` コマンドを利用してログインする

~~~
$ docker exec -ti practice-app /bin/sh
~~~

### List Docker Container

* 起動中のDockerコンテナは `docker ps` コマンドを利用して表示する

~~~
$ docker ps
~~~

* 停止中のDockerコンテナを含めて表示する場合は `-a` オプションを利用する

~~~
$ docker ps -a
~~~

### Debug Docker Container

* Dockerコンテナのログは `docker logs` コマンドを利用して表示する

~~~
$ docker logs practice-app
~~~

### Stop Docker Container

* Dockerコンテナは `docker stop` コマンドを利用して停止する

~~~
$ docker stop practice-app
~~~

### Remove Docker Container

* Dockerコンテナは `docker rm` コマンドを利用して削除する

~~~
$ docker rm practice-app
~~~

* 利用していない全てのDockerコンテナを削除するには、 `docker container prune` コマンドを利用する

~~~
$ docker container prune
~~~

### Remove Docker Image, Container, Volume and Network

* 利用していない全てのDockerイメージ、Dockerコンテナ、ボリューム、ネットワークを削除するには、 `docker system prune` を利用する

~~~
$ docker system prune --volumes
~~~
