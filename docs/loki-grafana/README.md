# Loki-Grafana

Følger guide:
https://cylab.be/blog/197/deploy-loki-on-kubernetes-and-monitor-the-logs-of-your-pods

https://nuculabs.dev/2022/02/17/container-log-monitoring-on-microk8s-with-loki-grafana-and-promtail/

Promtail-client:
https://sbcode.net/grafana/install-promtail-service/

```
helm install loki grafana/loki-stack --namespace loki --create-namespace --set grafana.enabled=true
```

```
helm upgrade --install loki grafana/loki-stack -n loki --set grafana.enabled=true
```

Issues:
Fikk problem:

"Error: Kubernetes cluster unreachable: Get "http://localhost:8080/version": dial tcp 127.0.0.1:8080: connect: connection refused"

Må da først kjøre denne:
```
kubectl config view --raw > ~/.kube/config
```

For å aksessere Grafana dashboard:

```
kubectl port-forward -n loki service/loki-grafana --address 0.0.0.0 3000:80
```


Går så til:
http://10.0.0.22:3000

----
You need to allow port 3100 through the firewall.

For Ubuntu (hvis man har aktivert firewall):

```
sudo ufw allow proto tcp from any to any port 3100
```


----

## Promtail client:

På raspberry pi:

```
cd /usr/local/bin
```
curl -O -L "https://github.com/grafana/loki/releases/download/v2.4.2/promtail-linux-arm64.zip"

```
unzip "promtail-linux-amd64.zip"
```

And allow the execute permission on the Promtail binary

```
sudo chmod a+x "promtail-linux-amd64"
```

Må legge til i /etc/hosts:

```
<IP-ADRESSE> loki.internal
```

Make a directory /etc/promtail and add our Promtail configuration (promtail.yml):

```
sudo nano /etc/promtail/promtail.yml
```

```
server:
  disable: true

positions:
  filename: /tmp/positions.yaml

clients:
  - url: http://10.0.0.22:3100/loki/api/v1/push #MERK HTTP I STEDET FOR HTTPS
    #tls_config:
      #ca_file: /etc/promtail/root_ca.crt
      #cert_file: /etc/promtail/promtail.crt
      #key_file: /etc/promtail/promtail.key

scrape_configs:
- job_name: system
  static_configs:
  - targets:
      - localhost
    labels:
      job: varlogs
      __path__: /var/log/*log
```

Alterantivt (som jeg ikke bruker nå):

```
server:
disable: true

positions:
filename: /tmp/positions.yaml

clients:
- url: https://loki.internal:3101/loki/api/v1/push
  #tls_config:
  #ca_file: /etc/promtail/root_ca.crt
  #cert_file: /etc/promtail/promtail.crt
  #key_file: /etc/promtail/promtail.key


scrape_configs:
- job_name: journal
  journal:
  max_age: 24h
  labels:
  job: systemd-journal
  relabel_configs:
    - source_labels: ['__journal__systemd_unit']
      target_label: 'unit'
    - source_labels: ['__journal__systemd_priority_keyword']
      target_label: 'priority'
    - source_labels: ['__journal__hostname']
      target_label: 'host'
```

-----

Create user specifically for the Promtail service


sudo useradd --system promtail
Create a file called promtail.service


sudo nano /etc/systemd/system/promtail.service
And add this script,

```
[Unit]
Description=Promtail service
After=network.target

[Service]
Type=simple
User=promtail
ExecStart=/usr/local/bin/promtail-linux-amd64 -config.file /usr/local/bin/config-promtail.yml

[Install]
WantedBy=multi-user.target
Now start and check the service is running.
```

Now run:

```
sudo systemctl enable --now promtail.service
```

```
sudo service promtail start
sudo service promtail status
```

We can now leave the new Promtail service running.

Now, since this example uses Promtail to read system log files, the promtail user won't yet have permissions to read them.

So add the user promtail to the adm group

```
usermod -a -G adm promtail
```

Verify that the user is non in the adm group


id promtail
If you ever need to stop the new Promtail service, then type

```
sudo service promtail stop
sudo service promtail status
```
Note it may take a minute to stop.

Warning

If you reboot your server, the Promtail Service may not restart automatically.

You can set the Promtail service to auto restart after reboot by entering,

sudo systemctl enable promtail.service

Port forwarding:
kubectl port-forward -n loki service/loki --address 0.0.0.0 3100:3100

Eksempel query i grafana for logg fra raspberry pi for endring av dns:
```
{job="varlogs"} |= "godaddy-dyndns"
{job="varlogs"} |= "godaddy-dyndns" |= "oyvindis.com"
{job="varlogs"} |= "godaddy-dyndns" |= "oyvindis.com" |= "Will update it to point to"
```