# お掃除

## 停止中のコンテナを削除する

<=1.12
```
# Linux の場合
docker rm `docker ps -f "status=exited" -q`

# Windows の場合
for /f %T in ('docker ps -f "status=exited" -q') DO docker rm %T
```

\>=1.13
```
docker container prune
```
* 確認プロンプトをスキップするには `-f` もしくは `--force` フラグをつける

## すべてのコンテナを削除する

```
# Linux の場合
docker rm `docker ps -a -q`

# Windows の場合
for /f %T in ('docker ps -a -q') DO docker rm %T
```

* `docker ps` では起動しているコンテナのみ表示されるので  
   停止しているコンテナも含めすべてのコンテナを表示するには `-a` をつける
* 事前にすべてのコンテナを停止しておくこと


## danglingイメージの削除する

<=1.12
```
# Linux の場合
docker rmi `docker images -q -f "dangling=true"`

# Windows の場合
for /f %T IN ('docker images -f "dangling=true" --format "{{.ID}}"') DO docker rmi %T
```

\>=1.13
```
# 条件なし削除
docker image prune

# 2020/10/30以前に作成されたイメージを削除する
docker image prune --filter until=2020-10-30

# 10分以上前に作成されたイメージを削除する
docker image prune --filter until=10m
```
* 確認プロンプトをスキップするには `-f` もしくは `--force` フラグをつける


## すべてのイメージを削除する

コンテナから使われていないイメージをすべて削除する

<=1.12
```
# Linux の場合
docker rmi `docker images -q`

# Windows の場合
for /f %T IN ('docker images -q') DO docker rmi %T
```

\>=1.13
```
docker image prune -a
```
* 確認プロンプトをスキップするには `-f` もしくは `--force` フラグをつける

## 未使用のVolumeを削除する

<=1.12
```
# Linux の場合
docker volume rm $(docker volume ls -q　-f dangling=true)

# Windows の場合
for /f %T IN ('docker volume ls -q -f "dangling=true"') DO docker volume rm %T
```

\>=1.13
```
docker volume prune
```
* 確認プロンプトをスキップするには `-f` もしくは `--force` フラグをつける

## 未使用のネットワークを削除する

\>=1.13
```
docker network prune
```
* 確認プロンプトをスキップするには `-f` もしくは `--force` フラグをつける

## 使用していない Docker オブジェクトの削除

未使用またはダングリングのすべてのイメージ、コンテナ、ボリューム、およびネットワークを削除する

* 停止コンテナ
* タグ無しイメージ
* 未使用ボリューム
* 未使用ネットワーク一括削除

\>=1.13
```
docker system prune

# volumeも破棄する場合は `--volumes` をつける
$ docker system prune --volumes

# 停止しているコンテナとすべての未使用のイメージを削除するには、`-a` をつける
docker system prune -a
```

* 確認プロンプトをスキップするには `-f` もしくは `--force` フラグをつける
* `<=17.06.0` では ボリュームも prune されていたが、`>=7.06.1` では `--volumes` フラグが必要になった


## 用語

  + dangling(ダングリング)
  
    ぶら下がっている（コンテナーに関連付けられていない）リソース（イメージ、コンテナー、ボリューム、およびネットワーク）

