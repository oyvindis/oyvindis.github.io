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

MERK: Etter at server bytter IP (fra Telenor), da må man oppdatere ip adresse:

```
nano /var/snap/microk8s/current/certs/csr.conf.template
```

Legg til ny ip-adresse og restart

```
sudo systemctl restart
```