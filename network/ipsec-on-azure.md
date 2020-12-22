This article refers to the article - [https://frankindev.com/2020/01/09/setup-ikev2-server-with-strongswan/]( https://frankindev.com/2020/01/09/setup-ikev2-server-with-strongswan/), but fixes some typos and configuration errors to make it truely available on Ubuntu OS of Azure Cloud Platform.

This project is born since I was not able to open the training courses at [https://web.microsoftstream.com](https://web.microsoftstream.com). The videos can't be loaded as a few underlying libraries are blocked.

**Pay More Attention:** this project only helps students that are forced to become members of the Zoom University to access academic resources - like tutorials, coursework, seminars from their schools. ***Never be evil***.

### step1. creat a virtual machine on Azure.

Normally, every student will have a free account with around $200 credit. The balance almostly can handle the 12-month cost of a low-performance machine.

This project uses **Ubuntu OS**.

### step2. execute the following instructions.

install dependency softwares.
```
sudo apt update
sudo apt install strongswan strongswan-pki libcharon-extra-plugins moreutils iptables-persistent
```

create certificates
```
PI=<Your Public IP>

mkdir -p ~/pki/{cacerts,certs,private}
chmod 700 ~/pki

pki --gen --type rsa --size 4096 --outform pem > ~/pki/private/ca-key.pem
pki --self --ca --lifetime 3650 --in ~/pki/private/ca-key.pem \
    --type rsa --dn "CN=$PI" --outform pem > ~/pki/cacerts/ca-cert.pem

pki --gen --type rsa --size 4096 --outform pem > ~/pki/private/server-key.pem
pki --pub --in ~/pki/private/server-key.pem --type rsa \
    | ipsec pki --issue --lifetime 1825 \
        --cacert ~/pki/cacerts/ca-cert.pem \
        --cakey ~/pki/private/ca-key.pem \
        --dn "CN=$PI" --san @$PI --san $PI \
        --flag serverAuth --flag ikeIntermediate --outform pem \
    >  ~/pki/certs/server-cert.pem

sudo cp -r ~/pki/* /etc/ipsec.d/
```

configure IPSec
```
sudo mv /etc/ipsec.conf{,.original}
sudo vim /etc/ipsec.conf
```

```
config setup
    charondebug="ike 1, knl 1, cfg 0"
    uniqueids=no

conn ikev2-strongswan
    auto=add
    compress=no
    type=tunnel
    keyexchange=ikev2
    fragmentation=yes
    forceencaps=yes
    dpdaction=clear
    dpddelay=300s
    rekey=no
    left=%any
    leftid=<Your Public IP>
    leftcert=server-cert.pem
    leftsendcert=always
    leftsubnet=0.0.0.0/0
    right=%any
    rightid=%any
    rightauth=eap-mschapv2
    rightsourceip=10.10.10.0/24
    rightdns=8.8.8.8,8.8.4.4
    rightsendcert=never
    eap_identity=%identity
    ike=chacha20poly1305-sha512-curve25519-prfsha512,aes256gcm16-sha384-prfsha384-ecp384,aes256-sha1-modp1024,aes128-sha1-modp1024,3des-sha1-modp1024!
    esp=chacha20poly1305-sha512,aes256gcm16-ecp384,aes256-sha256,aes256-sha1,3des-sha1!
```

create login account
```
sudo vim /etc/ipsec.secrets
```
```
: RSA "server-key.pem"
vpnacount : EAP "vpnpassword"
```

restart services
```
sudo ipsec reload
sudo ipsec restart
```

### step3. configure firewall and redirecting rules.

```
sudo ufw allow 500,4500/udp

sudo vim /etc/ufw/before.rules
```

pay attention that here is `eth0` network card, please change it as your need. *find `*filter` item and add the following content before and after it.*
```
*nat
-A POSTROUTING -s 10.10.10.0/24 -o eth0 -m policy --pol ipsec --dir out -j ACCEPT
-A POSTROUTING -s 10.10.10.0/24 -o eth0 -j MASQUERADE
COMMIT

*mangle
-A FORWARD --match policy --pol ipsec --dir in -s 10.10.10.0/24 -o eth0 -p tcp -m tcp --tcp-flags SYN,RST SYN -m tcpmss --mss 1361:1536 -j TCPMSS --set-mss 1360
COMMIT

# Don't delete these required lines, otherwise there will be errors
*filter
:ufw-before-input - [0:0]
:ufw-before-output - [0:0]
:ufw-before-forward - [0:0]
:ufw-not-local - [0:0]
# End required lines

-A ufw-before-forward --match policy --pol ipsec --dir in --proto esp -s 10.10.10.0/24 -j ACCEPT
-A ufw-before-forward --match policy --pol ipsec --dir out --proto esp -d 10.10.10.0/24 -j ACCEPT
```

configure redirecting rules. *find the following items and change their values*
```
sudo vim /etc/ufw/sysctl.conf
```
```
net/ipv4/ip_forward=1
net/ipv4/conf/all/accept_redirects=0
net/ipv4/conf/all/send_redirects=0
net/ipv4/ip_no_pmtu_disc=1
```

restart services
```
sudo ufw disable
sudo ufw enable
```

### step4. configure your clients.

Download `/etc/ipsec.d/cacerts/ca-cert.pem` to install it into your device.

The easiest way to download is to run `cat /etc/ipsec.d/cacerts/ca-cert.pem` and copy the content to paste it into a file with a `.pem` suffix.

Add a IKev2 VPN channel in your iOS or macOS and use `vpnaccount` and `vpnpassword` to login.
