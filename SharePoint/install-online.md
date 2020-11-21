## SharePoint Online 管理シェルをインストール

1. インストール
 
    ```
    > Install-Module -Name Microsoft.Online.SharePoint.PowerShell
    ```

1. 確認

    ```
    > (Get-InstalledModule -Name Microsoft.Online.SharePoint.PowerShell).Version

    Major  Minor  Build  Revision
    -----  -----  -----  --------
    16     0      20122  12000
    ```

    ```
    > Get-Module -Name Microsoft.Online.SharePoint.PowerShell -ListAvailable | Select Name,Version

    Name                                   Version
    ----                                   -------
    Microsoft.Online.SharePoint.PowerShell 16.0.20122.12000
    ```
