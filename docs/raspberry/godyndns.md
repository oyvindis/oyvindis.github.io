# Oppsett DIY dynamisk DNS

Bruker script fra https://github.com/sestus/godyndns/releases

```
mkdir godyndns
cd godyndns
```

```
sudo curl https://github.com/sestus/godyndns/releases/download/v1.0.3/godyndns-linux-arm64.tar.gz | tar -xz
```

```
wget -c https://github.com/sestus/godyndns/releases/download/v1.0.3/godyndns-linux-arm64.tar.gz -O - | tar -xz
```

Oppretter API-key i developer.godaddy.com

```
./godaddy-dyndns --api-key=<godaddy_api_key> --secret-key=<godaddy_secret_key> --domain=<godaddy_subdomain>
```

Følger beskrivelse: https://mikemylonakis.com/networking/poor-man's-dyndns-with-godaddy-domain/#use-the-go-script-to-update-the-domain

Create a systemd timerPermalink
Now let’s make sure this script runs periodically so that our public IP stays up to date. In order to do that:

Copy the script into /usr/local/bin :

```
sudo cp godaddy-dyndns /usr/local/bin/
```

Create the timer file ````/etc/systemd/system/five-minute-timer.timer```` with the following contents:

```
[Unit]
Description=Five Minute Timer

[Timer]
OnBootSec=5min
OnCalendar=*:0/5
Unit=five-minute-timer.service

[Install]
WantedBy=timers.target
```

Create the service unit file /etc/systemd/system/five-minute-timer.service with the following contents:

```
[Unit]
Description=Five Minute Timer Service

[Service]
Type=oneshot
Environment=GODADDY_API_KEY=<godaddy-api-key>
Environment=GODADDY_SECRET_KEY=<godaddy-secret-key>
Environment=GODADDY_DOMAIN=<godaddy-subdomain>
ExecStart=/usr/local/bin/godaddy-dyndns

[Install]
WantedBy=multi-user.target
```

Start and enable the timer:

```
systemctl enable five-minute-timer.timer && systemctl start five-minute-timer.timer
```

Verify that the timer runs every 5 minutes and check the logs:

```
systemctl list-timers
journalctl -u ten-minute-timer.service -f
```

Se logg:
```
ls -l /var/log
tail -f /var/log/syslog

```

Alle logger:
```
tail -f /var/log/*.log
```
