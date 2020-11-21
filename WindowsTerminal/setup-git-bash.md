## Windows Terminal で Git Bash を表示する

1. PowerShell を起動して適当な GUID を取得します。

    ```
    > [guid]::NewGuid()
    ```

1. Windows Terminal を起動し、[ 設定 ] から profiles.json を開く

1. `profiles` : [ { ... } ] の末尾に以下の追記する
   `guid` の値は先ほど取得した GUID に差し替える

    ```
    {

        "guid" : "{e635c1d7-d15f-f81e-4665-c60b3f27a3f6}",
        "acrylicOpacity" : 0.5,
        "closeOnExit" : true,
        "colorScheme" : "Campbell",
        "commandline" : "C:\\Program Files\\Git\\bin\\bash.exe",
        "cursorColor" : "#FFFFFF",
        "cursorShape" : "bar",
        "fontFace" : "consolas",
        "fontSize" : 14,
        "historySize" : 9001,
        "icon" : "C:\\Program Files\\Git\\mingw64\\share\\git\\git-for-windows.ico",
        "name" : "Git Bash",
        "padding" : "0, 0, 0, 0",
        "snapOnInput" : true,
        "startingDirectory" : "%USERPROFILE%",
        "useAcrylic" : true
    }
    ```

1. 新規タブの選択肢に `Git Bash` が追加された

1. 日本語対応
   `~/.bashrc` で環境変数 LANG を指定
   
     export LANG=ja_JP.UTF-8