# Letsencrypt

Følger installasjon: https://jonleopard.com/blog/microk8s-nginx-ingress-and-certbot-setup

```
kubectl apply -f https://github.com/jetstack/cert-manager/releases/download/v1.7.1/cert-manager.yaml
```
```
kubectl apply -f letsencrypt-staging.yaml
```

oppdaterer ingress.yaml med tls og annotations.
```
kubectl apply -f ingress.yaml
```

Sjekker sertifikat:
```
kubectl get certificate

kubectl describe certificate tls-secret
```

Deretter legge til prod-sertifikat:
Bytt inn prod i ingress.yaml

Se logg:
```
kubectl -n cert-manager logs cert-manager-5b6d4f8d44-wbtlz -f
```

Se challenges:
```
kubectl get challenges
```

Teste connection i samme namespace som cert-manager:
```
kubectl run -n cert-manager -it --rm --restart=Never busybox --image=busybox:1.28 -- nslookup acme-v02.api.letsencrypt.org
```

Mulig løsning?:
Your ingress is referring to an issuer, but the issuer is a ClusterIssuer. Could that be the reason? I have a similar setup with Issuer instead of a ClusterIssuer and it is working.

Noen tips:
https://timvanwassenhove.medium.com/notes-on-microk8s-and-cert-manager-87e1dbc1c98b

Prøver å slette:
```
kubectl delete pod -n cert-manager --all
```

Renewing av sertifikat feiler:

Kan sjekke disse kommandoene:

```
kubectl get clusterissuer
kubectl describe clusterissuer letsencrypt-prod

kubectl describe certificaterequest
kubectl describe order tls-secret-prod-4jkr4-1768495415
kubectl get challenges
kubectl describe challenge tls-secret-prod-4jkr4-1768495415-1131904813
```

If you do not open port 80 in your network security rules, then the order from cert-manager cannot be fulfilled. 
The order remains in pending state. Ideally you should not open your port 80 open always, 
you can choose to close this port once your order is fulfilled (you need to manage the renewal process after 90 days with the same process).

Så viktig å åpne port 80 i portåpninger:
Kalle den f.eks. Kub-4:
Protokoll: TCP
10.0.0.22
LAN: 80
WAN: 80

Prøve deretter å  slette pod-er under namespace cert-manager for å få den til å lage ordre på nytt:

One of my CertManager pods was frozen so I deleted them all and they restarted. The certs renewed immediately.

kubectl get pods -n cert-manager (or whatever namespace your pods are in)

Then delete them all.

kubectl delete pod -n cert-manager cert-manager-xxxx cert-manager-cainjector-xxxx cert-manager-webhook-xxxx:

Deretter restart:
sudo systemctl reboot

Så vente en stund og se om sertifikat renewes