## Windows Subsystem for Linux のインストール

1. CPU の Virtualization が有効化の確認
   [タスク マネージャー] → [パフォーマンス] 画面で [仮想化]が有効になっているのを確認する
   
1. Windows PowerShellを「管理者として実行する」

1. "Linux 用 Windows サブシステム" オプション機能を有効にする

   ```
   > dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
   
   展開イメージのサービスと管理ツール
   バージョン: 10.0.19041.1

   イメージのバージョン: 10.0.19041.264

   機能を有効にしています
   [==========================100.0%==========================]
   操作は正常に完了しました。
   ```

1. "仮想マシン プラットフォーム" のオプション コンポーネントを有効にする

   ```
   > dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart

   展開イメージのサービスと管理ツール
   バージョン: 10.0.19041.1

   イメージのバージョン: 10.0.19041.264

   機能を有効にしています
   [==========================100.0%==========================]
   操作は正常に完了しました。
   ```

1. Windowsを再起動

   ```
   > Restart-Computer
   ```


1. `Windows PowerShell` を開く

1. `WSL 2` を既定のバージョンとして設定する

   ```
   > wsl --set-default-version 2
   `WSL 2` を実行するには、カーネル コンポーネントの更新が必要です。詳細については https://aka.ms/wsl2kernel を参照してください
   ```

1. https://aka.ms/wsl2kernel に従い、Linux カーネル更新する

1. もう一度、`WSL 2` を既定のバージョンとして設定する

   ```
   > wsl --set-default-version 2
   WSL 2 との主な違いについては、https://aka.ms/wsl2 を参照してください
   ```

1. `Microsoft Store` を開き、`Linux` ディストリビューションをインストールする

   現在(2020/12/05)の `Windows Store` には3つの「Ubuntu」が存在する

   * 「Ubuntu」：現在の最も新しい「Ubuntu LTS」
      + 現在の「Ubuntu」は「Ubuntu 20.04」
      + 自動アップデートはされないので、下記のコマンドでアップデートする
         ```
         $ sudo do-release-upgrade
         ```
   * 「Ubuntu 18.04」：2023年04月まで利用可能なLTS版
   * 「Ubuntu 20.04」：2025年04月まで利用可能なLTS版

   
   ここでは、`Ubuntu 18.04 LTS` をインストールする

1. `Ubuntu` を起動する

   ユーザー アカウントとパスワードを設定する
   ```
   Installing, this may take a few minutes...
   Please create a default UNIX user account. The username does not need to match your Windows username.
   For more information visit: https://aka.ms/wslusers
   Enter new UNIX username: taro
   Enter new UNIX password:
   Retype new UNIX password:
   passwd: password updated successfully
   Installation successful!
   To run a command as administrator (user "root"), use "sudo <command>".
   See "man sudo_root" for details.
   ```

1. `Windows PowerShell` から `Ubuntu` が `WSL 2` で動いていることを確認する

   ```
   > wsl -l -v
      NAME            STATE           VERSION
   * Ubuntu-18.04    Running         2
   ```

1. `wsl` コマンドから `Linux` のコマンドが動作することを確認

   ```
   > wsl lsb_release -a
   No LSB modules are available.
   Distributor ID: Ubuntu
   Description:    Ubuntu 18.04.4 LTS
   Release:        18.04
   Codename:       bionic
   ```

a) `WSL 2` のCPU、メモリ割り当てをする
    [ユーザープロファイル]\.wslconfig 作成し、以下のように、CPUを2コア、メモリを2GB、swapwを0に割り当てる

   ※ https://docs.microsoft.com/ja-jp/windows/wsl/wsl-config#configure-global-options-with-wslconfig   
   ※ SSD等で大量のスワップIn/Outの発生を防ぐため、大量書き込みによるSSDの寿命を縮めることは回避

   ```
   [wsl2]
   memory=2GB
   processors=2
   swap=0
   ```

   `WSL` を再起動する
   ```
   # すべてのディストリビューション(軽量VM)を停止(wsl -tでも可)
   > wsl --shutdown

   # 個別のディストリビューションを停止
   > wsl -t "ディストリビューション名"
   ```

b) VMディスクサイズの調整
   WSLのディスクは仮想ディスク(.vhdx)のサイズが大きくなってきたら、
   手動で、ディスクを圧縮させる
   https://docs.microsoft.com/en-us/windows/wsl/compare-versions#expanding-the-size-of-your-wsl-2-virtual-hardware-disk

   ```
   # まずはディストリビューションの PackageFamilyName を取得
   > $packageName = Get-AppxPackage -Name "*ディストリ名*" | Select-Object -ExpandProperty PackageFamilyName
   > $packageName

   # 仮想ディスクは LOCALAPPDATA 配下の PackageFamilyName 下に保存されている
   > dir "$env:LOCALAPPDATA\Packages\$packageName\LocalState\*.vhdx"
   ```

   ```
   # 最初にディストリビューションを停止
   > wsl --shutdown

   # Hyper-Vの機能をインストールしていれば Optimize-VHD が使える
   Mount-VHD "仮想ディスク.vhdx" -ReadOnly -Passthru | Optimize-VHD Optimize-VHD -Mode Full -Passthru | Dismount-VHD

   # Hyper-Vの機能をインストールしていない場合は diskpart で
   > diskpart
   ```

   ```
   Microsoft DiskPart バージョン 10.0.19041.1

   Copyright (C) Microsoft Corporation.
   コンピューター: DESKTOP-39FAN60

   DISKPART>   
   DISKPART> select vdisk file="仮想ディスク.vhdx"
   DISKPART> attach vdisk readonly
   DISKPART> compact vdisk
   DISKPART> detach vdisk
   DISKPART> exit
   ```

c)  Windows側のPATHをPATH環境変数の設定から外す
   ディストリビューションコントロールから /etc/wsl.conf を開く
   https://docs.microsoft.com/ja-jp/windows/wsl/wsl-config#configure-per-distro-launch-settings-with-wslconf

   ```
   [interop]
   appendWindowsPath = false
   ```
   ディストリビューションを再起動
  
参考
  https://dev.classmethod.jp/articles/how-to-setup-wsl2/
