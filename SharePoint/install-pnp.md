
## PnP ライブラリをインストール

1. インストール

    ```
    > Install-Module PnP.PowerShell
    ```

2. 確認

    ```
    > (Get-InstalledModule -Name PnP.PowerShell).Version
    Major  Minor  Build  Revision
    -----  -----  -----  --------
    3      21     2005   1
    ```

    ```
    > Get-Module -Name PnP.PowerShell -ListAvailable | Select Name,Version

    Name                          Version
    ----                          -------
    SharePointPnPPowerShellOnline 3.21.2005.1
    ```
