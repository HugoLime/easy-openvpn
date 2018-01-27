# easy-openvpn
A simple template to set up a VPN with <a href="https://github.com/OpenVPN/openvpn"><img align="middle" height="48" src="http://swupdate.openvpn.net/community/icons/openvpn_logo.png"></a>

## 🔧 Installation

### 📡 On your VPS Server (Ubuntu 16.04)
- ⬇️ `sudo apt-get install git openvpn.server`
- 🔐 Create a Public Key Infrastructure (PKI):
  - `git clone https://github.com/OpenVPN/easy-rsa.git`
  - cd `easy-rsa/easyrsa3`
  - `./easyrsa init-pki`
  - Create a Certificate Authority (CA) with `./easyrsa build-ca` and choose a passphrase
  - Generate your server keys and certificate:
    - `./easyrsa gen-req server nopass`
    - `./easyrsa sign-req server server`
  - Generate your client keys and certificate:
    - `./easyrsa gen-req client nopass`
    - `./easyrsa sign-req client client`
  - Generate Diffie Hellman parameters with `./easyrsa gen-dh`
- 📝 Copy the following files to `/etc/openvpn`:
  - `server.conf` from the repo
  - `pki/ca.crt`
  - `pki/dh.pem`
  - `pki/issued/server.crt`
  - `pki/private/server.key`
- 🚀 Launch the OpenVPN service with `systemctl start openvpn@server`

### 💻 On your Client
- 📝 Get the `client.conf` file from the repo and change the server ip address
- ⬇️ Retrieve the following files from the server:
  - `pki/ca.crt`
  - `pki/issued/client.crt`
  - `pki/private/client.key`
- 🚀 Launch the OpenVPN client:
  - For MacOs User, you can install [Tunnelblick](https://tunnelblick.net/downloads.html)
  - For Linux user, you can install `sudo apt-get install openvpn`


## 🌍 Redirecting all traffic through the OpenVPN

### 📡 On your VPS Server
- Enable ip forwarding with `sudo bash -c 'echo 1 > /proc/sys/net/ipv4/ip_forward'`
- Set up a masquerading rule with `sudo iptables -t nat -A POSTROUTING -s 10.8.0.0/24 -o eth0 -j SNAT --to-source SERVER_IP_ADDRESS`
- Uncomment `push "redirect-gateway def1 bypass-dhcp"` in `server.conf`

### 💻 On your Client
- Check your ip address with `wget http://ipecho.net/plain -q -O - ; echo` or on [ipleak](http://ipleak.net/)
