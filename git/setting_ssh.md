## Windows環境でGitHubにssh接続する手順

1. Git Bash を起動する

2. .sshディレクトリに移動し中身を確認

    ```
    $ cd ~/.ssh
    $ ls
    known_hosts
    ```
3. 公開鍵・秘密鍵を作成する

    ```
    $ ssh-keygen -t rsa -C "your_email@youremail.com" -f github_id_rsa
    Generating public/private rsa key pair.
    Enter passphrase (empty for no passphrase):
    Enter same passphrase again:
    Your identification has been saved in github_id_rsa
    Your public key has been saved in github_id_rsa.pub
    The key fingerprint is:
    SHA256:******************************************* your_email@youremail.com
    The key's randomart image is:
    +---[RSA 3072]----+
    |                 |
    |          *      |
    |           *   * |
    |         *  * ***|
    |        * ** ****|
    |       * ********|
    |      *   *******|
    |     *   . ******|
    |        *********|
    +----[SHA256]-----+
    $ ls
    github_id_rsa  github_id_rsa.pub  known_hosts
    ```

4. ssh設定ファイルの編集

    ```
    ~/.ssh/config

    Host github.com
      User git
      Port 22
      HostName github.com
      IdentityFile ~/.ssh/github_id_rsa
      TCPKeepAlive yes
      IdentitiesOnly yes
    ```

5. GitHubに公開鍵を設定する

   生成した公開鍵(github_id_rsa.pub)をテキストエディタで開き、中身を全てコピーする
   GitHubにアクセスし、下記の手順通りに公開鍵を登録する

   1. GitHubにアクセスする
   2. [右上メニュー] → [Settings] をクリック
   3. [左メニュー] → [SSH and GPG keys] をクリック
   4. [New SSH Key] をクリック
   5. Title(自由)と、Key(コピーした内容をペースト)を入力して [Add SSH key] をクリック
   

6. 接続確認

    ```
    $ ssh -T github.com
    Enter passphrase for key '/c/Users/naoki/.ssh/github_id_rsa':
    Hi naokiss! You've successfully authenticated, but GitHub does not provide shell access.
    ```
