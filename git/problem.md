## 問題点

### sshでcloneするとエラーになる

* 動作環境
   + WSL2 環境 (Ubuntu 22.04.1 LTS)
   + OpenSSH_8.9p1 Ubuntu-3ubuntu0.1, OpenSSL 3.0.2 15 Mar 2022

* 事象
    + WSL2上から `github` のリポジトリを `clone` するときに、応答がない
    + `ssh -Tvvv git@github.com` で確認すると、以下で止まっていた

    ```
    debug3: Ignored env TERM
    debug3: Ignored env LESSOPEN
    debug3: Ignored env USER
    debug3: Ignored env DISPLAY
    debug3: Ignored env SHLVL
    debug3: Ignored env XDG_RUNTIME_DIR
    debug3: Ignored env WSLENV
    debug3: Ignored env XDG_DATA_DIRS
    debug3: Ignored env PATH
    debug3: Ignored env HOSTTYPE
    debug3: Ignored env PULSE_SERVER
    debug3: Ignored env WT_PROFILE_ID
    debug3: Ignored env OLDPWD
    debug3: Ignored env _
    debug2: channel 0: request shell confirm 1
    debug3: send packet: type 98
    debug2: channel_input_open_confirmation: channel 0: callback done
    debug2: channel 0: open confirm rwindow 32000 rmax 35000
    ```

* 解決策
    + SSH接続する時に、 IPQoS=cs1 を指定する
    
        ```sh
        ssh -Tvvv -o IPQoS=cs1 git@github.com
        ```

    + `~/.ssh/config` に `IPQoS` を設定する

        ```text:~/.ssh/config
        Host github.com
            IPQoS cs1
        ```

    ※ なお、 `cs1` 以外にも `0x00` や `none`, `lowdelay` でも drop されない模様。

* 参考
    + https://7ka.org/cant-connect-ssh-macos/
    + https://teratail.com/questions/249817?sip=n0070000_019&uid=38546
    + https://qiita.com/hoto17296/items/d8874a67b082dcc42516
    + https://man7.org/linux/man-pages/man5/ssh_config.5.html
    + https://chitoku.jp/computers/edgerouter-x-flets-dscp
    + https://teratail.com/questions/317631