# Hysteria with Fake SNI

This guide explains how to set up a Hysteria server using a self-signed certificate and fake SNI (Server Name Indication) to bypass DPI (Deep Packet Inspection) and masquerade traffic as allowed domains like Google, WhatsApp, Zoom, etc.

---

## 🚀 Server Installation

Run the following command to install Hysteria:

```bash
bash <(curl -fsSL https://get.hy2.sh/)
```

---

## ⚙️ Configuration

### 1. Edit server config file

File path: `/etc/hysteria/config.yaml`

Example configuration:

```yaml
listen: :443

tls:
  cert: /etc/hysteria/selfsigned.crt
  key: /etc/hysteria/selfsigned.key
  sni: domain.net

auth:
  type: password
  password: your-pass

masquerade:
  type: proxy
  proxy:
    url: https://domain.net/
    rewriteHost: true
```

> 🔒 **Note**: The `sni` field should match the domain you're trying to fake (e.g. `domain.net`). The certificate must also reflect this.

---

## 🔐 Creating a Self-Signed Certificate

To generate a self-signed certificate with a custom SNI, first create an `openssl.cnf` file:

```ini
[req]
distinguished_name = req_distinguished_name
x509_extensions = v3_req
prompt = no

[req_distinguished_name]
CN = domain.net

[v3_req]
keyUsage = keyEncipherment, dataEncipherment
extendedKeyUsage = serverAuth
subjectAltName = @alt_names

[alt_names]
DNS.1 = domain.net
```

Then generate the certificate:

```bash
openssl req -x509 -newkey rsa:2048 -nodes \
  -keyout /etc/hysteria/selfsigned.key \
  -out /etc/hysteria/selfsigned.crt \
  -days 365 \
  -config openssl.cnf
```

---

## 🔄 Starting the Service

Start and enable Hysteria server:

```bash
systemctl start hysteria-server.service
systemctl enable hysteria-server.service
```

---

## ✅ Tips

- Make sure ports 443 (or custom) are open on your firewall/VPS.
- Use real-looking domains to avoid detection.
- You can pair this with DPI bypass tools depending on your platform:
  - Windows: `v2rayN`, `zapret`, `tun2socks`
  - Android: `v2rayNG`, `tun2socks` (via apps like SocksDroid or custom VPNs)
  - iOS: `Shadowrocket` or `Stash` (support V2Ray protocols)
---

## 📌 Disclaimer

This setup is for educational and research purposes only. Misuse may violate your ISP or country's policies.
