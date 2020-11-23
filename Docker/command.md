# よく使うコマンド

## [`Docker Hub`](https://hub.docker.com/) からイメージを取得する

```
docker pull {イメージ名}[:{タグ名}]
```

e.g.)
```
docker pull node:14.15.1-slim
```

## `Dockerfile` からイメージを作成する

```
docker build [-t {イメージ名}[:{タグ名}]] {Dockerfileがあるディレクトリ}
```
* -t : `名前:タグ` 形式で名前とオプションのタグを指定

e.g.)
```
docker build -t hoge/node:1.0 ./sample-app
```

## コンテナを起動する

```
docker run --name {コンテナ名} -it {イメージ名} /bin/bash
```

## コンテナを起動し、終了と同時にコンテナを削除する

```
docker run --rm --name {コンテナ名} -it {イメージ名} /bin/bash
```

## バックグラウンドで実行する

```
docker run --name {コンテナ名} -it -d {イメージ名} /bin/bash
```

## 実行中のコンテナを確認する

```
docker ps -a
```

## コンテナを起動する

停止中コンテナを起動する

```
docker start {コンテナID}
   or
docker start {コンテナ名}
```

## コンテナを停止する

```
docker stop {コンテナID}
   or
docker stop {コンテナ名}
```

## コンテナに入る

```
docker exec -it {コンテナ名} bash
```

## コンテナを指定してホストOS上でコマンドを実行する

```
docker exec -it {コンテナ名} python --version
```

## コンテナを削除する

```
docker rm -f {コンテナID}
  or
docker rm -f {コンテナ名}
```

## イメージを一覧表示する

```
docker images
```

## イメージを削除する

```
docker rmi {イメージID}
```

## すべてのコンテナを停止する

```
docker stop $(docker ps -q)
```
* `-q` を付けることでコンテナのIDのみを取得する
