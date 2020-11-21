## 実行ポリシーの設定

* PowerShellで実行ポリシーを確認
    ```
    > Get-ExecutionPolicy
    ```

* PowerShellで実行ポリシーを変更
    ```
    > Set-ExecutionPolicy RemoteSigned -Scope CurrentUser -Confirm
    ```
