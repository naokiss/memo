## Ubuntu セットアップ

1. 日本のサーバーから更新ファイルを取得するよう設定を変更する

    ```
    $ sudo sed -i 's/\/archive\.ubuntu/\/jp\.archive\.ubuntu/' /etc/apt/sources.list
    ```

1. パッケージの更新とアップグレード

    ```
    $ sudo apt -y update
    $ sudo apt -y upgrade
    ```

1. 日本語言語パックのインストール

    ```
    $ sudo apt -y install language-pack-ja
    ```

1. ロケールを日本語に設定

    ```
    $ sudo update-locale LANG=ja_JP.UTF-8
    ```
    
    ※英語に戻したい場合は
    ```
    $ sudo update-locale LANG=en_US.UTF8
    ```

1. Ubuntu を再起動

    特定のディストリを終了
    
    ```
    > wsl --terminate <ディストリ名>
          or 
    > wsl -t <ディストリ名>
    ```

    ディストリビューションのステータス確認
    ```
    > wsl -l -v
    ```

    すべてのディストリを終了
    ```
    > wsl --shutdown
    ```


    ```
    > wsl --terminate Ubuntu-18.04
    ```

    ```
    > wsl -l -v
    ```

1. Ubuntu を再起動後の確認

    設定を確認
    ```
    $ locale
    LANG=ja_JP.UTF-8
    LANGUAGE=
    LC_CTYPE="ja_JP.UTF-8"
    LC_NUMERIC="ja_JP.UTF-8"
    LC_TIME="ja_JP.UTF-8"
    LC_COLLATE="ja_JP.UTF-8"
    LC_MONETARY="ja_JP.UTF-8"
    LC_MESSAGES="ja_JP.UTF-8"
    LC_PAPER="ja_JP.UTF-8"
    LC_NAME="ja_JP.UTF-8"
    LC_ADDRESS="ja_JP.UTF-8"
    LC_TELEPHONE="ja_JP.UTF-8"
    LC_MEASUREMENT="ja_JP.UTF-8"
    LC_IDENTIFICATION="ja_JP.UTF-8"
    LC_ALL=
    ```

    ```
    $ echo $LANG
    ja_JP.UTF-8
    ```

    ```
    $ date
    2020年 11月  1日 日曜日 19:42:29 JST
    ```

1. タイムゾーン設定を日本時間（JST）にする

    ```
    $ sudo dpkg-reconfigure tzdata
    ```

1. 日本語マニュアルのインストール

    ```
    $ sudo apt -y install manpages-ja manpages-ja-dev
    ```

1. WSL の PATH から Windows の PATH を削除する

    ```
    $ sudo vi /etc/wsl.conf
    ```

    以下のように記載する
    ```
    [interop]
    appendWindowsPath=false
    ```
    
    WSLを再起動する
