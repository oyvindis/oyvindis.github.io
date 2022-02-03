# Set up Wireguard vpn on Raspberry Pi

## Installasjon
Følger installasjonsbeskrivelse: https://pimylifeup.com/raspberry-pi-wireguard/

## Relevante kommandoer for Wireguard vpn

Legge til client:
```
pivpn add
```

Se liste:
```
pivpn list
```

Fjerne client:
```
pivpn -r <client-name>
```

Endrer profilen og legger til www.oyvindis.com, i stedet for ip-adresse

Kopiere profil til klient:
```
scp ubuntu@anton1:/home/ubuntu/configs/<navn på profil> .
```