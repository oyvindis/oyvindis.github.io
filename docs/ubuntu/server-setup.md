# Server setup

### Følger beskrivelse for innstallasjon på MacBook Pro (early 2012):
https://ubuntu.com/server/docs/installation

evt. se også:
https://medium.com/mkdir-awesome/how-to-install-and-set-up-ubuntu-20-04-on-an-old-macbook-pro-6d8e4fe9f5f3

### Etter installasjon, for å få wireless til å fungere:

Start med oppdatere ubuntu:
```
Sudo apt upgrade
```

Sjekke status:
```
$ uname -a
```
```
$ hostnamectl
```

Finne wireless interface:
```
$ ls /sys/class/net enp0s25  lo  wlp3s0
```

Fant ikke det i første omgang, må da installere driver (på kablet nett):

```
sudo apt install firmware-b43-installer
```

Installed and got the WI-fi working by just typing on the terminal:

```
sudo apt-get install bcmwl-kernel-source
```

and after type 

```
sudo modprobe wl to load the file
```

Kjør deretter 

```
ls /sys/class/net
```

og se at «wlp3s0» finnes.

Legg til wireless-konfig:
```
nano /etc/netplan/00-installer-config.yaml
```

```
network:
  ethernets:
    enp2s0f0:
      dhcp4: true
  version: 2
  wifis:
    wlp3s0:
      access-points:
        "<SSID wifi>":
          password: "<Trådløs wifi passord>"
      dhcp4: true
```

```
netplan apply
```

Kjør:
```
sudo apt install <disse pakkene under>
```

wpasupplicant library, along with its two own dependencies: libnl-route-3-200 and libpcsclite1
```
netplan apply
```

Slå av skjermen:
Try adding consoleblank=60 or any mount of seconds that you want to the GRUB_CMDLINE_LINUX_DEFAULT= line in the /etc/default/grub file.
Then run 
```
sudo update-grub
```
and reboot the system
```
sudo systemctl reboot
```

Configure Lid Close Behavior Of Your Laptop With Ubuntu 20.04 LTS
```
sudo gedit /etc/systemd/logind.conf
```

Now, you need to search the line #HandleLidSwitch=suspend


## Oppdatere Linux

```
sudo apt update
```

```
apt list --upgradable
```

```
sudo apt upgrade
```

Best way to get your Ubuntu up-to-date should be (as one command, so that one is executed automatically after the last)

```
sudo apt update && sudo apt full-upgrade && sudo apt autoremove
```

Hope this helps

Otherwise

```
sudo apt list --upgradable && sudo apt upgrade
```