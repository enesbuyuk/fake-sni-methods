# V2Ray with Fake SNI

This document explains how to configure V2Ray with a fake SNI (Server Name Indication) using a self-signed certificate to bypass Deep Packet Inspection (DPI).

---

## 📦 Installation

### 1. Install V2Ray

Run the following command to install V2Ray:

```bash
bash <(curl -L https://raw.githubusercontent.com/v2fly/fhs-install-v2ray/master/install-release.sh)
```

---

## 🔐 Generate SSL Certificate for Fake SNI

Create a self-signed SSL certificate with the following command:

```bash
sudo openssl req -x509 -newkey rsa:4096 -nodes \
  -keyout /usr/local/etc/v2ray/v2ray-key.pem \
  -out /usr/local/etc/v2ray/v2ray-cert.pem \
  -days 365 \
  -subj "/CN=example.com"
```

---

## 🔒 Set File Permissions

Adjust permissions for certificate and key files:

```bash
sudo chown nobody:nogroup /usr/local/etc/v2ray/v2ray-cert.pem
sudo chown nobody:nogroup /usr/local/etc/v2ray/v2ray-key.pem
sudo chmod 600 /usr/local/etc/v2ray/v2ray-key.pem
sudo chmod 644 /usr/local/etc/v2ray/v2ray-cert.pem
```

---

## ⚙️ Configure V2Ray

Create the configuration file at `/usr/local/etc/v2ray/config.json` with the following content:

```json
{
  "inbounds": [
    {
      "port": YOUR_PORT,
      "protocol": "vmess",
      "settings": {
        "clients": [
          {
            "id": "YOUR_UUID_HERE",
            "alterId": 0
          }
        ]
      },
      "streamSettings": {
        "network": "tcp",
        "security": "tls",
        "tlsSettings": {
          "serverName": "example.com",
          "certificates": [
            {
              "certificateFile": "/usr/local/etc/v2ray/v2ray-cert.pem",
              "keyFile": "/usr/local/etc/v2ray/v2ray-key.pem"
            }
          ]
        }
      }
    }
  ],
  "outbounds": [
    {
      "protocol": "freedom",
      "settings": {}
    }
  ]
}
```

Replace `YOUR_PORT`, `example.com` and, `YOUR_UUID_HERE` with your own values.

---

## ▶️ Enable and Start V2Ray

Use the following commands to enable and start the V2Ray service:

```bash
sudo systemctl enable v2ray
sudo systemctl start v2ray
```

---

## 💡 Notes

- Make sure `serverName` matches the domain in the certificate (e.g., `example.com`).
- You can use Fake SNI to mimic allowed domains like WhatsApp, Zoom, etc.
- For client-side DPI evasion, pair this with tools like:
  - **Windows:** `v2rayN`, `zapret`, `tun2socks`
  - **Android:** `v2rayNG`, `tun2socks` (via SocksDroid, ToyVPN, etc.)
  - **iOS:** `Shadowrocket`, `Stash`

---

## ⚠️ Disclaimer

This setup is for educational and research purposes only. Misuse may violate your local laws or ISP terms.
