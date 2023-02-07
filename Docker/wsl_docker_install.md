# `WSL2` 上に `Docker` をインストールする

## `Docker`インストール

1. パッケージ インデックス&パッケージ一覧をアップデート

    ```sh
    sudo apt update
    ```
    ```sh
    sudo apt upgrade
    ```

2. パッケージのインストール

    ```sh
    sudo apt-get install ca-certificates curl gnupg lsb-release
    ```

3. Docker の公式 GPG キーを追加

    ```sh
    sudo mkdir -p /etc/apt/keyrings
    ```
    ```sh
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
    ```

4. Dockerリポジトリ情報の更新する

    ```sh
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
    ```

5. aptパッケージ インデックスを更新

    ```sh
    sudo apt-get update
    ```

6. Docker Engine(Docker Engine、containerd、および Docker Compose) のインストール

    ```sh
    sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin
    ```

7. Docker service 起動

    ```sh
    sudo service docker start
    ```

8. 動作確認

    ```sh
    sudo docker run hello-world
    ```

9. Docker Engine の権限設定

    sudo 無しでdockerを実行させるため、Docker Group にユーザーを所属させる。

    ```sh
    sudo usermod -aG docker $USER
    ```

※ 一旦 exit でログアウトする。

## Docker Compose インストール

1. インストールディレクトリを作成し、Docker Composeをダウンロードする

    ```sh
    sudo mkdir -p /usr/local/lib/docker/cli-plugins
    ```

    ```sh
    sudo curl -SL "https://github.com/docker/compose/releases/download/v2.4.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/lib/docker/cli-plugins/docker-compose
    ```

2. 実行権限を付与する

    ```sh
    sudo chmod +x /usr/local/lib/docker/cli-plugins/docker-compose
    ```

3. シンボリックリンクを作成する

    ```sh
    sudo ln -s /usr/local/lib/docker/cli-plugins/docker-compose /usr/bin/docker-compose
    ```

4. バージョン確認

    ```sh
    $ docker-compose --version
    Docker Compose version v2.4.1
    ```
