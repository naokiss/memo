# `anyenv` の導入 

**env 系をまとめて管理できるツール

## 環境

* Ubuntu 18.04

## 事前準備

* `git` をインストールする

    ```
    $ sudo apt install git -y
    ```

## `anyenv` をインストールする

* `Github` から clone する
    ```
    $ git clone https://github.com/anyenv/anyenv ~/.anyenv
    ```

* `anyenv` に `PATH` を設定する
    ```
    $ echo 'export PATH="$HOME/.anyenv/bin:$PATH"' >> ~/.profile
    $ echo 'eval "$(anyenv init -)"' >> ~/.profile
    ```

* シェルをリフレッシュ

    ```
    $ exec $SHELL -l
    ```

* ターミナルに、以下のエラーが表示される

    ```
    ANYENV_DEFINITION_ROOT(/home/taro/.config/anyenv/anyenv-install) doesn't exist. You can initialize it by:
    anyenv install --init
    ```

    指示通り、`anyenv` の初期化を実行する
    ```
    $ anyenv install --init
    Manifest directory doesn't exist: /home/taro/.config/anyenv/anyenv-install
    Do you want to checkout ? [y/N]: y
    Cloning https://github.com/anyenv/anyenv-install.git master to /home/taro/.config/anyenv/anyenv-install...
    Cloning into '/home/taro/.config/anyenv/anyenv-install'...
    remote: Enumerating objects: 5, done.
    remote: Counting objects: 100% (5/5), done.
    remote: Compressing objects: 100% (5/5), done.
    remote: Total 62 (delta 1), reused 0 (delta 0), pack-reused 57
    Unpacking objects: 100% (62/62), done.
    ```

* バージョン確認
    ```
    $ anyenv -v
    anyenv 1.1.2-1-g67d402f
    ```

* `anyenv` でインストール可能な `**env系` を一覧を表示する
    ```
    $ anyenv install -l
    crenv
    denv
    erlenv
    exenv
    goenv
    hsenv
    jenv
    jlenv
    luaenv
    nodenv
    phpenv
    plenv
    pyenv
    rbenv
    Renv
    sbtenv
    scalaenv
    swiftenv
    tfenv
    ```

## `anyenv` の `Plugin` をインストールする

* `plugins`ディレクトリを `.anyenv` 配下に作成する

    ```
    $ mkdir -p ~/.anyenv/plugins
    ```

* `anyenv-update` プラグインのインストール

    `**env系` のアップデートなどを一括でアップデートするプラグイン
    ```
    $ git clone https://github.com/znz/anyenv-update.git ~/.anyenv/plugins/anyenv-update
    ```

    アップデート
    ```
    $ anyenv update
    ```

* `anyenv-git` プラグインをインストールする

    `env系`の全てのgitコマンドを実行するプラグイン
    ```
    $ git clone https://github.com/znz/anyenv-git.git ~/.anyenv/plugins/anyenv-git
    ```

    ```
    # インストールした全ての`**env系`とインストールされているプラグインのアップデート
    $ anyenv git pull

    # ガーベージコレクション
    $ anyenv git gc

    # 全てのリモートリポジトリを表示
    $ anyenv git remote -v

    # gitのステータス表示
    $ anyenv git status
    ```

* `**env` をインストールする

    `nodenv` をインストールする
    ```
    $ anyenv install nodenv
    ```

    インストールしたnodenvを有効にするため、シェルの再起動
    ```
    $ exec $SHELL
    ```

* インストールした `**env系` のバージョンの確認

    ```
    $ anuenv versions
    nodenv:
    Warning: no Node detected on the system
    ```
    `node` がインストールしていないので上記のように警告が表示

    `nodenv` を使い、異なるバージョンをインストールするしてみる

    インストールできるバージョンの確認
    ```
    $ nodenv install -l
       :
    10.1.0
    10.2.0
    10.2.1
    10.3.0
       :
    ```

    `node` バージョン `8.10.0` と `10.1.0` をインストールする
    ```
    $ nodenv install 8.10.0
    $ nodenv install 10.1.0
    $ nodenv rehash
    ```
    シェルを再起動
    ```
    $ exec $SHELL -l
    ```

    もう一度バージョン確認
    ```
    $ anyenv versions
    nodenv:
      8.10.0
      10.1.0
    ```

    グルーバルのバージョン変更

    システムの全てのバージョンが指定される
    ```
    $ nodenv global 10.1.0
    $ anyenv versions
    nodenv:
      8.10.0
    * 10.1.0 (set by /home/taro/.anyenv/envs/nodenv/version)
    ```

    ローカルのバージョン変更

    特定のプロジェクト（ディレクトリ配下）でバージョンが指定される
    ```
    $ mkdir hoge
    $ cd hoge
    $ nodenv local 8.10.0
    $ anyenv versions
    nodenv:
    * 8.10.0 (set by /home/taro/hoge/.node-version)
      10.1.0
    ```

    node のアンインストール
    ```
    nodenv uninstall 10.1.0
    ```