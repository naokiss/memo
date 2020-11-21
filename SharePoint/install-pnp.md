
## PnP ライブラリをインストール

1. インストール

    ```
    > Install-Module SharePointPnPPowerShellOnline 
    ```

2. 確認

    ```
    > (Get-InstalledModule -Name SharePointPnPPowerShellOnline).Version
    Major  Minor  Build  Revision
    -----  -----  -----  --------
    3      21     2005   1
    ```

    ```
    > Get-Module -Name SharePointPnPPowerShellOnline -ListAvailable | Select Name,Version

    Name                          Version
    ----                          -------
    SharePointPnPPowerShellOnline 3.21.2005.1
    ```
