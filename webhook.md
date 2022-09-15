# Setting up Webhook

Guide to setting up webhook service on Linux (?)
https://github.com/adnanh/webhook

## Installation

- Find the latest version's tarball from https://github.com/adnanh/webhook/releases for your system's architecture
- Download the tarball:
   
  `curl -LO https://github.com/adnanh/webhook/releases/download/<WEBHOOK_VERSION>/webhook-linux-amd64.tar.gz`

- Unpack it

  `tar -xvf webhook-linux-amd64.tar.gz`

- Move `webhook` binary to `/usr/local/bin`

## Configure

- Open port `9000` in ufw: `sudo ufw allow 9000/tcp comment 'Webhook Service'`
  
- Create a config directory for webhook: 

  `sudo mkdir -p /etc/webhook`

- Create a `hooks.yml` in the config directory (https://github.com/adnanh/webhook#configuration)
  
- Create a scripts directory for webhook:
  
  `sudo mkdir -p /var/opt/webhook/scripts`

   - Note to self: previous installation at `/var/webhook/scripts` in one of my servers

## Systemd

- Create a user for the webhook service

  `sudo useradd --no-create-home --shell /bin/false webhook`

- (Optional) Add webhook user to docker group to allow webhook scripts to run docker commands

  `sudo usermod -aG docker webhook`

- Create systemd service file

  ```
  [Unit]
  Description=Webhook Service
  Wants=network-online.target
  After=network-online.target

  [Service]
  User=webhook
  Restart=on-failure
  ExecStart=/usr/local/bin/webhook -hooks /etc/webhook/hooks.yml -verbose

  [Install]
  WantedBy=multi-user.target
  ```

- Enable webhook to start on reboot

  `sudo systemctl enable webhook.service`

- Restart systemd 

  `sudo systemctl daemon-reload`

- Start webhook service

  `sudo systemctl start webhook.service`


