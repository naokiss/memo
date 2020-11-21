## RouterOS で NAT Router を構築する

### 構成

* Router Board : hEX
* OS : MikroTik RouterOS
* Firmware : 6.47.7
* WAN 側
    + ポート : ether1
    + ipv4 firewall : 有効
    + NAT : 有効
    + DHCP Client : 有効
* LAN 側
    + ポート : ether2 ～ ether5 ( bridge 構成 )
    + IP Address : 192.168.10.1/24
    + Gateway : 192.168.10.1
    + DHCP Server : 有効
        - Pool : 192.168.10.50 - 192.168.10.99
        - Gateway : 192.168.10.1
    + DNS Server : 有効
        - IP Address: 192.168.10.1

### 事前準備

1. `winbox.exe` で `RouterOS` に接続し、Terminal を開く

1. 現在の構成をリセットする

    ```
    [admin@MikroTik] > /system reset-configuration no-defaults=yes 
    Dangerous! Reset anyway? [y/N]:
    y
    system configuration will be reset
    ```

### 構築


1. `winbox.exe` で `RouterOS` に再接続する

1. Terminal を開く

1. LAN 側 のブリッジを作成する

    ```
    /interface bridge
    add comment=lan-bridge admin-mac=XX:XX:XX:XX:XX:XX auto-mac=no name=bridge
    ```
    * comment : コメント
    * admin-mac : `Router Board` の `ether2` の振られている Mac アドレスを指定する
    * auto-mac : 静的 Mac アドレスを指定するので `no` を指定する
    * name : ブリッジの名前

1. インターフェイス管理するためのインターフェイスセットを定義する

    WAN 側のインターフェースセットと LAN 側インターフェースセットを定義する

    ```
    /interface list
    add comment=wan-interfaces name=WAN
    add comment=lan-interfaces name=LAN
    ```
    * comment : コメント
    * name : インターフェイスセットの名前

1. インターフェースセットのメンバーとして、インターフェースを追加する

    * インターフェース `bridge` を LAN 側インターフェースセット `LAN` に追加する
    * インターフェース `ether1` を WAN 側インターフェースセット `WAN` に追加する
    ```
    /interface list member
    add comment=lan-member interface=bridge list=LAN
    add comment=wan-member interface=ether1 list=WAN
    ```
    * comment: コメント
    * interface : インターフェースを指定する
    * list : インターフェースセットを指定する

1. LAN 側のブリッジグループ を作成する

    インタフェース `ether2`, `ether3`, `ether4`, `ether5` をグループ化してブリッジを構成する
    ```
    /interface bridge port
    add comment=lan-bridge bridge=bridge interface=ether2
    add comment=lan-bridge bridge=bridge interface=ether3
    add comment=lan-bridge bridge=bridge interface=ether4
    add comment=lan-bridge bridge=bridge interface=ether5
    ```
    * comment: コメント
    * bridge : ブリッジを指定する
    * interface : インターフェースを指定する

1. WAN 側のネットワークを設定する

    LAN 側のインターフェース `ether1` に DHCP クライアントの設定をする

    ```
    /ip dhcp-client
    add comment=wan-dhcp-client dhcp-options=hostname,clientid disabled=no interface=ether1
    ```
    * comment : コメント
    * dhcp-options : DHCPサーバーに送信されるオプション (ホスト名、MACアドレス)
    * disabled : 有効にするので、`no` を指定する
    * interface : DHCPクライアントが実行されるインターフェイス

1. LAN 側のネットワークを設定する

    LAN 側のブリッジ `bridge` にIPアドレスを設定する
    ```
    /ip address
    add comment=lan-address address=192.168.10.1/24 interface=bridge network=192.168.10.0
    ```
    * comment : コメント
    * address : IPアドレスを指定する
    * interface : IPアドレスが割り当てられているインターフェースを指定する
    * network : ネットワークのIPアドレスを指定する

1. LAN 側の IPアドレス プール ( 割り当てるIPアドレス範囲 ) を追加する

    ```
    /ip pool
    add comment=lan-pool ranges=192.168.10.50-192.168.10.99 name=lan-pool
    ```
    * comment : コメント
    * ranges : 割り当てるIPアドレス範囲
    * name : プールの名前

1. DHCP サーバーのネットワークを設定する

    ```
    /ip dhcp-server network
    add comment=lan-dhcp address=192.168.10.0/24 dns-server=192.168.10.1 gateway=192.168.10.1
    ```
    * comment : コメント
    * address : DHCP で IPアドレスの割り当てを行うサブネットを指定する
    * dns-server : デフォルト DNSサーバーのIPアドレスを指定する
    * gateway : デフォルトゲートウェイのIPアドレスを指定する

1. DHCP Server を追加する

    LAN 側の DHCPサーバーが機能するインターフェイス ( `bridge` ) を指定し、上で作成したIPアドレス プールを割り当てる
    ```
    /ip dhcp-server
    add address-pool=lan-pool disabled=no interface=bridge name=lan-dhcp
    ```
    * address-pool : IPアドレスを取得する pool `lan-dhcp` を指定する
    * disabled : 有効にするので、`no` を指定する
    * interface : DHCP Server が実行される インターフェース `bridge` を指定する
    * name : DHCP サーバーの名前

1. DNS サーバーへのリモートリクエストを許可する

    LAN 側のインターフェースからDNS サーバーへのアクセスを許可する
    ```
    /ip dns
    set allow-remote-requests=yes
    ```
    * allow-remote-requests : リモートリクエストを許可するので `yes` を指定する   
      `no` の場合は、`MikroTik` (内部プロセス)からのみアクセス可能
    * `yes` にすると、WAN 側からもアクセスできるのでファイアウォールフィルターでブロックすること

1. DNS に LAN 側の IP アドレスを登録する

    LAN 側の IP アドレスを `router.lan` というホスト名で、DNSレコードを追加する
    ```
    /ip dns static
    add comment=lan-hostname address=192.168.10.1 name=router.lan
    ```
    * comment : コメント
    * address : DNSレコードする IPアドレス
    * name : DNSレコードするホスト名

1. NAT を有効にする

    ```
    /ip firewall nat
    add comment="masquerade" action=masquerade chain=srcnat ipsec-policy=\
    out,none out-interface-list=WAN
    ```
    * comment : コメント

1. ファイヤーウォールの設定

    ```
    /ip firewall filter
    add action=drop chain=input comment="Block DNS" dst-port=53
    protocol=udp in-interface=all-ppp
    add action=drop chain=input comment="Block DNS" dst-port=53
    protocol=tcp in-interface=all-ppp
    add action=accept chain=input comment=\
        "defconf: accept established,related,untracked" connection-state=\
        established,related,untracked
    add action=drop chain=input comment="defconf: drop invalid" connection-state=\
        invalid
    add action=accept chain=input comment="defconf: accept ICMP" protocol=icmp
    add action=accept chain=input comment=\
        "defconf: accept to local loopback (for CAPsMAN)" dst-address=127.0.0.1
    add action=drop chain=input comment="defconf: drop all not coming from LAN" \
        in-interface-list=!LAN
    add action=accept chain=forward comment="defconf: accept in ipsec policy" \
        ipsec-policy=in,ipsec
    add action=accept chain=forward comment="defconf: accept out ipsec policy" \
        ipsec-policy=out,ipsec
    add action=fasttrack-connection chain=forward comment="defconf: fasttrack" \
        connection-state=established,related
    add action=accept chain=forward comment=\
        "defconf: accept established,related, untracked" connection-state=\
        established,related,untracked
    add action=drop chain=forward comment="defconf: drop invalid" \
        connection-state=invalid
    add action=drop chain=forward comment=\
        "defconf: drop all from WAN not DSTNATed" connection-nat-state=!dstnat \
        connection-state=new in-interface-list=WAN
    ```
    * すでに許可されている接続、新しくかつ許可された接続のINPUTチェインパケットを許可
    * 無効な接続のINPUTチェインパケットを拒否
    * ICMPプロトコルINPUTチェインパケットを許可
    * ループバックアドレス(127.0.0.1)からのINPUTチェインパケットを許可
    * LANインターフェース以外からのINPUTチェインパケットを拒否

    説明はあとで追記

1. 一般ユーザー用グループを追加する

    ```
    /user group
    add name=general policy=local,ssh,winbox,read skin=default
    ```
    * name : グループ名
    * policy : 許可する権限を指定する
        + local : コンソールから、ローカルにログインを許可する
        + ssh : ssh から、ログインを許可する
        + winbox : WinBox から、ログインを許可する
        + read : ルーターの構成の読み取りを許可する

1. ユーザーを登録する

    `admin` という名前は狙われるやすいので、別のユーザー名のユーザーを作成する
    ```
    /user
    add address=192.168.10.0/24 disabled=no group=full password=******** name=taro
    ```
    * address : 許可するネットワーク
    * disabled : 有効にするので、`no` を指定する
    * group : グループを指定する ( `admin` ユーザーの変更なので `full` を指定する)
    * password : パスワードを指定する
    * name : ユーザー名を指定する

1. `admin` ユーザーを無効にする

    ```
    /user
    set admin disabled=yes
    ```
    * disabled : `admin` ユーザーを無効にするので `yes` を指定する

1. ネイバー(近隣探索)の設定

    LAN インターフェースセットにネイバーを設定する
    ```
    /ip neighbor discovery-settings
    set discover-interface-list=LAN
    ```
    * discover-interface-list : 近隣探索を有効にするインターフェースセットを指定する   
      MACアドレスで `RouterOS` を探せるようにして、`winbox.exe` から接続できるようにするため

1. タイムゾーンを設定する

    ```
    /system clock
    set time-zone-name=Asia/Tokyo
    ```
    * time-zone-name : タイムゾーンに日本を指定する

1. MAC Telnet Server への接続を LAN 側からのみ許可する

    MACベースのプロトコルを使用して、ルーターへのアクセスを提供するために使用される   
    2つの `MikroTik` `RouterOS` ルーター間でのみ可能
    ```
    /tool mac-server
    set allowed-interface-list=LAN
    ```
    * allowed-interface-list : MAC telnet 接続は、LANインターフェースセットのみ許可する

1. MAC WinBox Server への接続を LAN 側からのみ許可する

    MACベースのプロトコルを使用して `Winbox` からルーターへのアクセスを提供するために使用される   
    
    ```
    /tool mac-server mac-winbox
    set allowed-interface-list=LAN
    ```
    * allowed-interface-list : MAC WinBox 接続は、LANインターフェースセットのみ許可する

1. サービスの設定

    不要なサービスを停止、または、アクセスをLANインターフェースのみ許可する
    ```
    > /ip service
    set api disabled=yes 
    set api-ssl disabled=yes 
    set ftp disabled=yes
    set ssh address=192.168.10.0/24
    set telnet disabled=yes
    set winbox address=192.168.10.0/24
    set www address=192.168.10.0/24
    ```
    * api サービスを無効にする
    * api-ssl サービスを無効にする
    * ftp サービスを無効にする
    * ssh サービスを 172.16.10.0/24 のネットワークからのみ許可する
    * telnet サービスを無効にする
    * winbox サービスを 172.16.10.0/24 のネットワークからのみ許可する
    * www サービスを 172.16.10.0/24 のネットワークからのみ許可する

---------------------------------------
## 参考

* [RouterOS - RouterOS - MikroTik Documentation](https://help.mikrotik.com/docs/display/ROS/RouterOS)
* [Manual:TOC - MikroTik Wiki](https://wiki.mikrotik.com/wiki/Manual:TOC)
* [Введение в Layer 3 Firewall MikroTik](https://itnan.ru/post.php?c=1&p=435070)

