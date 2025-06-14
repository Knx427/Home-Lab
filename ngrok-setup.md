
# ngrok Setup for Remote SSH Access Behind CGNAT Domain & Systemd Boot Script

This guide walks you through setting up ngrok to automatically start at boot with a static domain and log the public address.

## Why ngrok?

- ISP CGNAT blocks inbound port forwarding.
- ngrok creates a secure tunnel through its servers.
- Easy remote access without router changes.
---

## 1. Installation (if not already installed)

```bash
sudo apt install ngrok -y
```
## Setup

1. Sign up at https://ngrok.com and get your Auth Token & static Domain.
2. Authenticate ngrok:

```bash
ngrok authtoken YOUR_AUTH_TOKEN
```

---

## 📁 2. Ngrok Configuration (`ngrok.yml`)
### Note: You should have configured Nextcloud to continue with the steps below, see [nextcloud-config](nextcloud.md)        
         
---

Edit or create this config:

```bash
sudo nano ~/snap/ngrok/current/.config/ngrok/ngrok.yml
```

Example content:

```yaml
version: 3
tunnels:
    nextcloud:
        proto: http  # proto only accepts http, configure the port for https
        addr: 80  # 443 for https 
        domain: [Your-static-Domain-Name].ngrok-free.app
        auth: "[Username]:[Password]" # You can add an extra Protection Layer redirecting to an login screen before allowing passage
    ssh:
        proto: tcp
        addr: 5555  # Your SSH port
agent:
    authtoken: [Your-Auth-Token]  # Should already be there because of previous step
```

Replace values accordingly.

Test it:

```bash
ngrok start --all --config ~/snap/ngrok/current/.config/ngrok/ngrok.yml
```
Use the forwarded address to connect remotely:
```bash
ssh youruser@0.tcp.ngrok.io -p PORT
```
and test in your browser your domain:  

```
http[s]://[Your-Static-Domain-Name].ngrok-free.app
```
---

## 📜 3. Startup notify Script

Create a script to fetch urls and send them to your email:

```bash
sudo nano ~/bin/ngrok-notify.sh
```

Contents:

```bash
#!/bin/bash

# Wait for ngrok to fully initialize
sleep 10

# Get ngrok public URLs
URLS=$(curl -s http://127.0.0.1:4040/api/tunnels | jq -r '.tunnels[] | .public_url')

# Save to a file (optional)
echo "$URLS" > "$HOME/ngrok_urls.txt"

# Send email with the URLs
echo -e "Ngrok tunnels are up:\n\n$URLS" | mail -s "Ngrok URLs for SSH & Nextcloud" [your@email.com]

```

Make it executable:

```bash
chmod +x ~/bin/ngrok-notify.sh
```

---

## ⚙️ 4. Create systemd  Services

Create the `ngrok.service`:
```bash
sudo nano /etc/systemd/system/ngrok.service
```
Content:
```bash
[Unit]
Description=Ngrok Tunnel for SSH and Nextcloud
After=network-online.target
Wants=network-online.target

[Service]
ExecStart=/snap/ngrok/current/ngrok start --all --config=/home/[user]/snap/ngrok/current/.config/ngrok/ngrok.yml
WorkingDirectory=/home/[user]
Restart=on-failure
User=[user]

[Install]
WantedBy=multi-user.target

```
Create a one-shot service:

```bash
sudo nano /etc/systemd/system/ngrok-notify.service
```

Contents:

```ini
[Unit]
Description=Send ngrok URLs via email after tunnels are up
After=ngrok.service  # Runs only when ngrok.service is active 
Requires=ngrok.service

[Service]
ExecStart=/home/[user]/ngrok_notify.sh
Type=oneshot
User=[user]

[Install]
WantedBy=multi-user.target

```

Enable and start:

```bash
sudo systemctl daemon-reexec
sudo systemctl daemon-reload
sudo systemctl enable ngrok.service
sudo systemctl enable ngrok-notify.service
sudo systemctl start ngrok.service
sudo systemctl start ngrok-notify.service
```

---

## 🔍 5. Monitor and Log Output

```bash
systemctl status ngrok.service
ps -ef | grep ngrok  # You should see one PID with the ngrok.service execution path
```

---
Done! Ngrok will now tunnel your Nextcloud via your free static domain and auto-start at boot + You get the URLs sent to your email.
## Note
- Never Share your public Ngrok domain/URLs!
