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
