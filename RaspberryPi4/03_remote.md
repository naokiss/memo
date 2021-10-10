# Raspberry Pi にリモートから接続する

## 目次

- [Raspberry Pi OS の OS イメージを作成](01_install.md)
- [Raspberry Pi OS のセットアップ](02_setup.md)
- Raspberry Pi にリモートから接続する
- [Raspberry Pi 4 Model B のファームウェアをアップデート](04_firmware.md)
- [Raspberry Pi OS でスクリーンショットを撮る](99_screenshot.md)

## SSH でリモート接続

### SSH の有効化 (CUI で有効化する場合)

1. ssh をサービス起動デーモンとして有効化する

   ```sh
   sudo systemctl enable ssh
   ```

1. ssh サービスを起動する

   ```sh
   sudo systemctl start ssh
   ```

### SSH の有効化 (GUI で有効化する場合)

1. Raspberry Pi Software Configuration Tool を起動する

   ```sh
   sudo raspi-config
   ```

   ![](images/remote/1-1.png)

1. [Interfacing Options] を選択する

   ![](images/remote/1-2.png)

1. [P2 SSH] を選択する

   ![](images/remote/1-3.png)

1. [はい] を選択する

   ![](images/remote/1-4.png)

1. [了解] を選択する

   ![](images/remote/1-5.png)

1. [Finish] を選択する

   ![](images/remote/1-6.png)

### 接続確認

![](images/remote/1-7.png)

## VNC でリモート接続

### VNC の有効化 (CUI で有効化する場合)

1. vncserver をサービス起動デーモンとして有効化する

   ```sh
   sudo systemctl enable vncserver-x11-serviced
   ```

1. ssh サービスを起動する

   ```sh
   sudo systemctl start ssh
   ```

### VNC の有効化 (GUI で有効化する場合)

1. Raspberry Pi Software Configuration Tool を起動する

   ```sh
   sudo raspi-config
   ```

   ![](images/remote/2-1.png)

1. [Interfacing Options] を選択する

   ![](images/remote/2-2.png)

1. [P3 VNC] を選択する

   ![](images/remote/2-3.png)

1. [はい] を選択する

   ![](images/remote/2-4.png)

1. [了解] を選択する

   ![](images/remote/2-5.png)

1. [Finish] を選択する

   ![](images/remote/2-6.png)

### 接続確認

1. [VNC Viewer](https://www.realvnc.com/en/connect/download/viewer/) をダウンロードして、起動する

   初回起動時は以下のような画面が表示されます。使用統計データを送りたくない場合は「Send anonymous usage data to help improve VNC Viewer」のチェックを外す

   「GOT IT」をクリックして画面を閉じる

   ![](images/remote/2-7.png)

1. Raspberry Pi の IP アドレスを入力して、Enter を押下する

   ![](images/remote/2-8.png)

1. 初めて接続するデバイスに関しては警告がでるので、[Continue] をクリックする

   ![](images/remote/2-9.png)

1. 接続情報を入力して、[OK] をクリックする

   - [Username] : pi
   - [Password] : 設定したパスワード

     ![](images/remote/2-10.png)

1. Raspberry Pi OS の画面が表示された

   ![](images/remote/2-11.png)

## RDP(Remote Desktop Protocol) でリモート接続

1. xrdp パッケージをインストールする

   ```sh
   sudo apt-get install xrdp
   ```

1. xrdp サービスを起動する

   ```sh
   sudo service xrdp restart
   ```

1. 接続確認

   リモートデスクトップを開いて、Raspberry Pi の IP アドレスを入力する

   ![](images/remote/3-1.png)

   [はい] をクリックする

   ![](images/remote/3-2.png)

   接続情報を入力して、[OK] をクリックする

   - [Session] : Xorg
   - [username] : pi
   - [password] : 設定したパスワード

   ![](images/remote/3-3.png)

   Raspberry Pi OS の画面が表示された

   ![](images/remote/3-4.png)

## X Window System でリモート接続

1. [VcXsrv](https://sourceforge.net/projects/vcxsrv/) をインストールする

   [Next] をクリックする

   ![](images/remote/4-1.png)

   [Install] をクリックする

   ![](images/remote/4-2.png)

2. XLaunch を起動する

   デフォルトの選択肢のまま [次へ] をクリックする

   ![](images/remote/4-3.png)

   デフォルトの選択肢のまま [次へ] をクリックする

   ![](images/remote/4-4.png)

   デフォルトでチェックが外れている[Disable access control] にチェックをつけて、[次へ] をクリックする

   ![](images/remote/4-5.png)

   Disable access control のチェックが外れたままだと、GUI のアプリケーションを起動する時にエラーメッセージが表示される

   ![](images/remote/4-6.png)

   [完了]をクリックする

   ![](images/remote/4-7.png)

3. XLaunch が起動したら、Windows Defender ファイアウォールの警告が表れるので、  
   「プライベート ネットワーク (ホームネットワークや社内ネットワークなど)」のみを  
   許可する

   ![](images/remote/4-8.png)

4. Raspberry Pi に SSH でログインする

5. DISPLAY 環境変数の設定する

   「xxx.xxx.xxx.xxx」に XLaunch が実行されているマシンの IP アドレスを指定する

   ```sh
   pi@raspberrypi:~ $export DISPLAY=xxx.xxx.xxx.xxx:0.0
   ```

6. 疎通確認

   ```sh
   pi@raspberrypi:~ $ xeyes
   ```

   お目目さんが表示されれば、ok

   ![](images/remote/4-9.png)
