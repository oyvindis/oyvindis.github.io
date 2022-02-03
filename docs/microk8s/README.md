# Microk8s

## Install docker
https://blog.cherryservers.com/how-to-install-and-start-using-docker-on-ubuntu-20.04
```
sudo usermod -aG docker ${USER}
```

## Install Microk8s

https://ubuntu.com/tutorials/install-a-local-kubernetes-with-microk8s#4-accessing-the-kubernetes-dashboard

RESTARTER SERVER ETTER SLETTET MICROK8S
```
sudo systemctl reboot
```
```
sudo snap install microk8s --classic
```
```
microk8s enable dns
microk8s enable storage
microk8s enable ingress
```

```
sudo systemctl reboot
```

Create banana.yaml:

```
kubectl apply -f banana.yaml
```

Check endpoint:
```
kubectl get endpoints
```

Create ingress.yaml:

```
kubectl apply -f ingress.yaml
```

se at det er tilgjengelig på
www.oyvindis.com/banana


Enable dashboard:

```
microk8s enable dashboard
```

```
kubectl get ns
```

For å bruke dashboard fra f.eks. annen Mac på samme nettverk:
```
microk8s dashboard-proxy
```

Visit:
https://10.0.0.22:10443

Stopping Microk8s
```
microk8s stop
```
Execute the command below when you need to start Microk8s and its services again for further testing.

Starting Microk8s
```
microk8s start
```

But if you no longer need MicroK8s and wish to uninstall it from your computer, run the removal command below.

Uninstalling microk8s
```
sudo snap remove microk8s
```

Diverse kommandoer
```
kubectl get all -A
```

```
kubectl create namespace oyvindis

kubectl delete namespace oyvindis

kubectl delete service <service-name>
```

KUBE_CONFIG:

I github må det opprettes en secret som heter KUBE_CONFIG, slik:

```
ssh anton2

kubectl config view --raw
```

Kopiere ut denne, endre server til oyvindis.com:16443

```
server: https://oyvindis.com:16443
```

MERK: uten "www".

Kopier til secret "KUBE_CONFIG" i github-repo.


MERK: Etter at server bytter IP (fra Telenor), da må man oppdatere ip adresse:

Gjør dette:

```
nano /var/snap/microk8s/current/certs/csr.conf.template
```

Legg til ny ip-adresse (IP.99) og reboot:

```
sudo systemctl reboot
```

Pass på at ikke firewall stopper på 16443 for å deploye:

enten disable ufw

```
sudo ufw disable
```

eller åpne for porten

```
sudo ufw allow 16443/tcp
```

Feilmelding i github:

Deploy to Kubernetes cluster
Unable to connect to the server: dial tcp 31.45.112.26:16443: i/o timeout
Error: Error: Unable to connect to the server: dial tcp 31.45.112.26:16443: i/o timeout

Sjekk om www.oyvindis.com er oppdatert til ny ip-adresse. 
Må da muligens kopiere ut config fra kubernetes på nytt og oppdatere github-secret.