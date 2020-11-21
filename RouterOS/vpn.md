## RouterOS でL2TP/IPSecサーバ構築

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
* VPN
    + L2TP/IPsec
    + DHCP サーバー : 192.168.10.100 - 192.168.10.109
    
### 事前準備

[RouterOS で NAT Router を構築する](nat.md) を参考にNATルーターを構築する

### 構築

1. `winbox.exe` で RouterOS に接続し、Terminal を開く

1. VPN 用 DHCP プール(割り当てるIPアドレス範囲)を追加する

    ```
    /ip pool
    add name=vpn-pool ranges=192.168.10.100-192.168.10.109
    ```

1. L2TP 用の PPP Profile を作成

    ```
    /ppp profile
    add name=vpn-profile change-tcp-mss=yes local-address=192.168.10.1 remote-address=vpn-pool dns-server=8.8.8.8 use-encryption=yes
    ```

1. L2TP over IPsec の有効化

    ```
    /interface l2tp-server server
    set authentication=mschap2 default-profile=vpn-profile enabled=yes ipsec-secret=1234567890 max-mru=1460 max-mtu=1460 use-ipsec=yes
    ```

1. IPsec の設定

    ```
    /ip ipsec proposal
    add name=vpn-proposal auth-algorithms=sha1 enc-algorithms=3des,aes-256-cbc pfs-group=none
    ```

1. IPsec 用のユーザ作成

    ```
    /ppp secret
    add name=user1 password=0987654321 service=l2tp profile=vpn-profile
    ```

1. ファイヤーウォール設定

    ```
    /ip firewall filter
    add chain=input action=accept comment="VPN L2TP UDP 500" in-interface=ether1 protocol=udp dst-port=500 place-before=4
    add chain=input action=accept comment="VPN L2TP UDP 1701" in-interface=ether1 protocol=udp dst-port=1701 place-before=4
    add chain=input action=accept comment="VPN L2TP UDP 4500" in-interface=ether1 protocol=udp dst-port=4500 place-before=4
    add chain=input action=accept comment="VPN L2TP ESP" in-interface=ether1 protocol=ipsec-esp place-before=4
    add chain=input action=accept comment="VPN L2TP AH" in-interface=ether1 protocol=ipsec-ah place-before=4
    add action=drop chain=input in-interface=ether1　place-before=4
    ```


--------------------------------
VPNで使用するポート暗号
* L2TP/IPsec　ポート番号
* AHパケット　（プロトコル番号 51）
* ESPパケット（プロトコル番号 50）
* UDP 500（ISAKMP/UDP）
* UDP 1701（L2TP/UDP）
* UDP 4500（IPsec-NAT-T/UDP）