## git 初期設定

- 設定ファイル  

    * system
        + 全ユーザに関する設定
        + <gitインストールフォルダ>\etc\gitconfig ( `%PROGRAMDATA%\Git\gitconfig` )
            ```
            git config -l --system
            ```

    * global
        + ユーザに関する設定
        + <ユーザーのホームフォルダ>\\.gitconfig ( `%USERPROFILE%\.gitconfig`)
            ```
            git config -l --global
            ```

    * local
        + 特定のディレクトリ (リポジトリ) に関する設定
        + <ローカルリポジトリ>\\.git\\config

            ```
            git config -l --local
            ```

- config ファイルの場所の確認

    ```
    git config -l --show-origin
    ```

- ユーザー情報の設定   

    ```
    git config --global user.name "First-name Family-name"
    git config --global user.email "username@example.com"
    ```

- エディタの設定

    ```
    git config --global core.editor "'c:/Program Files/sakura/sakura.exe' -CODE=4"
    ```

- 自動改行コード(CrLf)変換の設定

    ```
    git config --global core.autocrlf false
    git config --global core.safecrlf false
    ```

- 日本語のファイルやフォルダ名が数値に置換設定

    ```
    git config --global core.quotepath false
    ```

- 差分比較・マージの設定

    config

    ```
    [diff]
        tool = WinMerge
    [difftool "WinMerge"]
        path = C:/Program Files/WinMerge/WinMergeU.exe
        cmd = \"C:/Program Files/WinMerge/WinMergeU.exe\" -f \"*.*\" -e -u -r \"$LOCAL\" \"$REMOTE\"
    [merge]
        tool = WinMerge
    [mergetool "WinMerge"]
        path = C:/Program Files/WinMerge/WinMergeU.exe
        cmd = \"C:/Program Files/WinMerge/WinMergeU.exe\" -e -u \"$LOCAL\" \"$REMOTE\" \"$MERGED\"
    [alias]
        windiff = difftool -y -d -t WinMerge
        winmerge = mergetool -y -t WinMerge
    ```

- fast-forward の設定

    ```
    git config --global --add merge.ff false
    git config --global --add pull.ff only
    ```

- git log文字化け対策   

    ```
    git config --global core.pager "LESSCHARSET=utf-8 less"
    ```