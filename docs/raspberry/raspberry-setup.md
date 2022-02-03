# Oppsett av Raspberry Pi

## Installasjon Ubuntu server

Guide for å lage en boot disk på sd-kort
https://ubuntu.com/tutorials/how-to-install-ubuntu-on-your-raspberry-pi#1-overview

## Endre hostname
Change Hostname on Ubuntu 20.04 – Alternative Method (Reboot Required)
Another way to permanently change the hostname is by editing two configuration files:

/etc/hostname
/etc/hosts
The changes take effect immediately after system reboot.

Step 1: Open /etc/hostname and Change the Hostname
Edit the file with a text editor of your choice. In this example, we will be using the Vim editor:
```
sudo vi /etc/hostname
```

The /etc/hostname file contains only the current hostname. Replace it with your new choice.

Step 2: Open /etc/hosts and Change the Hostname

```
sudo vi /etc/hosts
```

The file /etc/hosts maps hostnames to IP addresses. Look for the hostname you wish to change and simply replace it with your new choice.

Step 3: Reboot the System
Reboot your computer to apply the changes:

```
sudo systemctl reboot
```

## Tilgang ssh fra lokal mac

```
nano ~/.ssh/config
```

Legg til:
```
Host anton1
HostName anton1
User ubuntu
```

```
ssh-copy-id -i ~/.ssh/id_ed25519.pub anton1
```

Koble til med ssh:
```
ssh anton1
```

## Firewall

Først oppdatere:

```
sudo apt update
sudo apt upgrade
```

Evt.
```
sudo apt full-upgrade
```

Server:
```
sudo apt-get update
sudo apt-get upgrade
sudo apt-get dist-upgrade
```

Port 51820 for Wireguard (anton1)

Port 22 for ssh
Port 10443 dashboard

```
sudo ufw default allow outgoing
sudo ufw default deny incoming
sudo ufw allow ssh
```

```
sudo ufw enable
```

Gi tilgang til dashboard:
```
sudo ufw allow from 10.0.0.1/24 to any port 10443 proto tcp
```

Gi tilgang ssh på anton1:

```
sudo ufw allow from 10.0.0.1/24 to any port 22 proto tcp
```

Gi tilgang til vpn på anton1:

```
sudo ufw allow 51820/udp
```
