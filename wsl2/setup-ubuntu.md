## Ubuntu セットアップ

1. 日本のサーバーから更新ファイルを取得するよう設定を変更する

   ```bash
   sudo sed -i 's/\/archive\.ubuntu/\/jp\.archive\.ubuntu/' /etc/apt/sources.list
   ```

1. パッケージの更新とアップグレード

   ```bash
   sudo apt -y update
   sudo apt -y upgrade
   ```

1. 日本語言語パックのインストール

   ```bash
   sudo apt -y install language-pack-ja
   ```

1. ロケールを日本語に設定

   ```bash
   sudo update-locale LANG=ja_JP.UTF-8
   ```

   ※英語に戻したい場合は

   ```bash
   sudo update-locale LANG=en_US.UTF8
   ```

1. Ubuntu を再起動

   特定のディストリを終了

   ```cmd
   wsl --terminate <ディストリ名>
         or
   wsl -t <ディストリ名>
   ```

   ディストリビューションのステータス確認

   ```cmd
   wsl -l -v
   ```

   すべてのディストリを終了

   ```cmd
   wsl --shutdown
   ```

   ```cmd
   wsl --terminate Ubuntu-18.04
   ```

   ```cmd
   wsl -l -v
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
   echo $LANG
   ja_JP.UTF-8
   ```

   ```bash
   date
   2020年 11月  1日 日曜日 19:42:29 JST
   ```

1. タイムゾーン設定を日本時間（JST）にする

   ```bash
   sudo dpkg-reconfigure tzdata
   ```

1. 日本語マニュアルのインストール

   ```bash
   sudo apt -y install manpages-ja manpages-ja-dev
   ```

1. `systemd` を有効にする

   `/etc/wsl.conf` に以下を追記

   ```ini
   [boot]
   systemd=true
   ```

1. WSL の PATH から Windows の PATH を削除する

   `/etc/wsl.conf` に以下のように記載する

   ```ini
   [interop]
   appendWindowsPath=false
   ```

1. パーミッションの設定

   `/etc/wsl.conf` に以下を追記

   ```ini
   [automount]
   enabled = true
   options = "metadata, umask=22, fmask=11"
   mountFsTab = false
   ```

   `~/.profile` に以下を追記

   ```bash
   if [ "$(umask)" = "0000" ]; then
      umask 0022
   fi
   ```

1. sshでパスフレーズを省略

   `~/.bashrc` に以下を追記

   ```bash
   SSH_ENV=~/.ssh-agent
   if [ -f ~/.ssh-agent ]; then
      . ~/.ssh-agent
   fi
   if [ -z "$SSH_AGENT_PID" ] || ! kill -0 $SSH_AGENT_PID >& /dev/null; then
      ssh-agent | sed 's/^echo/#echo/' > $SSH_ENV
      chmod 600 $SSH_ENV
      . $SSH_ENV
   fi
   ssh-add -l >& /dev/null || ssh-add
   ```

1. ビープ音を消す

   - コマンドラインのビープ音を消す

     `~/.inputrc` に以下を追記

     ```conf
     set bell-style none
     ```

   - Vim のビープ音を消す

     `~/.vimrc` に以下を追記

     ```conf
     set belloff=all
     ```

1. WSL を再起動する

   ```cmd
   wsl --shutdown
   ```
