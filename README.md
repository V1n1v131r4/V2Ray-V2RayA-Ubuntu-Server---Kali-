# V2Ray-V2RayA-Ubuntu-Server---Kali-
Instalação e Configuração do V2Ray + v2rayA (2026)

# Atualizar sistema
`sudo apt update && sudo apt upgrade -y`

# Instalar V2Ray
`curl -Ls https://raw.githubusercontent.com/v2fly/fhs-install-v2ray/master/install-release.sh | sudo bash`

# Gerar UUID
`cat /proc/sys/kernel/random/uuid`   


# Configurar domínio:

Crie shadow.bunqrlabs.com apontando para o IP do servidor. 
Obter certificado TLS:

```
sudo apt install certbot -y
sudo certbot certonly --standalone -d dominio.com --non-interactive --agree-tos -m seu-email@dominio.com
```

Ajustar permissões:

```
sudo install -d -o nobody -g nogroup /etc/ssl/v2ray/
sudo install -m 644 -o nobody -g nogroup /etc/letsencrypt/live/dominio.com/fullchain.pem /etc/ssl/v2ray/
sudo install -m 600 -o nobody -g nogroup /etc/letsencrypt/live/dominio.com/privkey.pem /etc/ssl/v2ray/
```

Editar configuração:

```
sudo nano /usr/local/etc/v2ray/config.json
```
```
{
  "inbounds": [{
    "port": 443,
    "protocol": "vmess",
    "settings": {
      "clients": [
        { "id": "SEU_ID", "alterId": 0 }
      ]
    },
    "streamSettings": {
      "network": "ws",
      "security": "tls",
      "tlsSettings": {
        "certificates": [
          {
            "certificateFile": "/etc/ssl/v2ray/fullchain.pem",
            "keyFile": "/etc/ssl/v2ray/privkey.pem"
          }
        ]
      },
      "wsSettings": {
        "path": "/v2ray"
      }
    }
  }],
  "outbounds": [{
    "protocol": "freedom"
  }]
}
```
Habilitar e iniciar:
```
sudo systemctl enable v2ray
sudo systemctl start v2ray
sudo ufw allow 443/tcp
```


# Cliente Kali Linux
## Instalar snapd
```
sudo apt install snapd -y
sudo systemctl enable --now snapd apparmor
```
# Instalar v2rayA
```
sudo snap install v2raya
```
# Conectar permissões
```
sudo snap connect v2raya:firewall-control
sudo snap connect v2raya:network-control
sudo snap start v2raya
```

Acessar: http://127.0.0.1:2017

Configurar servidor:
```
Protocol: vmess
Address: dominio.com
Port: 443
ID: SEU_ID
AlterID: 0
TLS: on
Network: ws
Host (WS): dominio.com
Path: /v2ray
```
Configurar proxy:
```
Settings → Transparent Proxy → On
Forward DNS Requests: on
Transparent Proxy Implementation: TProxy
Traffic Splitting Mode: Proxy All Traffic
Mode: Follow Transparent Proxy 
Salvar → Selecionar servidor → Start
```
✅ Teste em https://dnsleaktest.com
