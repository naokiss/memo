## Ubuntu セットアップ

1. 日本のサーバーから更新ファイルを取得するよう設定を変更する

   ```bash
   $ sudo sed -i 's/\/archive\.ubuntu/\/jp\.archive\.ubuntu/' /etc/apt/sources.list
   ```

1. パッケージの更新とアップグレード

   ```bash
   $ sudo apt -y update
   $ sudo apt -y upgrade
   ```

1. 日本語言語パックのインストール

   ```bash
   $ sudo apt -y install language-pack-ja
   ```

1. ロケールを日本語に設定

   ```bash
   $ sudo update-locale LANG=ja_JP.UTF-8
   ```

   ※英語に戻したい場合は

   ```bash
   $ sudo update-locale LANG=en_US.UTF8
   ```

1. Ubuntu を再起動

   特定のディストリを終了

   ```cmd
   > wsl --terminate <ディストリ名>
         or
   > wsl -t <ディストリ名>
   ```

   ディストリビューションのステータス確認

   ```cmd
   > wsl -l -v
   ```

   すべてのディストリを終了

   ```cmd
   > wsl --shutdown
   ```

   ```cmd
   > wsl --terminate Ubuntu-18.04
   ```

   ```cmd
   > wsl -l -v
   ```

1. Ubuntu を再起動後の確認

   設定を確認

   ```bash
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

   ```bash
   $ echo $LANG
   ja_JP.UTF-8
   ```

   ```bash
   $ date
   2020年 11月  1日 日曜日 19:42:29 JST
   ```

1. タイムゾーン設定を日本時間（JST）にする

   ```bash
   $ sudo dpkg-reconfigure tzdata
   ```

1. 日本語マニュアルのインストール

   ```bash
   $ sudo apt -y install manpages-ja manpages-ja-dev
   ```

1. WSL の PATH から Windows の PATH を削除する

   ```bash
   $ sudo vi /etc/wsl.conf
   ```

   以下のように記載する

   ```text
   [interop]
   appendWindowsPath=false
   ```

1. ビープ音を消す

   - コマンドラインのビープ音を消す
     `~/.inputrc` にに以下を追記

     ```bash
     set bell-style none
     ```

   - Vim のビープ音を消す
     `~/.vimrc` に以下を追記
     ```bash
     set belloff=all
     ```

1. WSL を再起動する
