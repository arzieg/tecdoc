---
title: ssh jump und port forwarding
subtitle:  ssh jump und port forwarding zwischen Client und Server
date: 2024-12-08
tags: ["ssh", "ssh-jump", "port forwarding", "local port forwarding", "remote port forwarding"]
---


# Jumphost


Immer dann, wenn es keine direkte Verbindung zwischen Client und Server gibt, aber eine Verbindung über ein oder mehrere Jump-Server eine Verbinung aufgebaut werden kann, kann mit der -J Option im ssh-Befehl der Server erreicht werden. Mit der Option -J werden 1 bis n Jumphosts angegeben, über die dann eine Verbindung zum Zielserver aufgebaut werden. Je Jumphost kann man username, Jumphost und Port des ssh-Daemon angeben. 

Allgemein: 

```
ssh -J username@jumphost1:port1,username@jumphost2:port2 -A username@zielhost:port
```

Der Port kann weggelassen werden, wenn der Default Port 22 angesprochen wird. 
Username kann weggelassen werden, wenn man sich mit seinem login User über die Jumphosts einloggen kann. 

Noch angenehmer wird das ganze, wenn mit einem ssh-agent gearbeitet wird, bei dem der private Key auf dem Client hinterlegt ist. Sollte man sich öfter auf dem Remote-Server einloggen wollen, bietet es sich an, ein Alias in ~/.ssh/config zu definieren. Bspw.:

```
Host myserver
  -J username@jumphost1:port1,username@jumphost2:port2 -A username@zielhost:port
  ForwardAgent yes
```

Der Server ist dann unter ssh myserver über die Jumpserver erreichbar.


![Einfacher Jump](../images/ssh-jumpserver1.png)

# Local Portforwarding

Beim local Portforwarding wird ein entfernter Port auf dem Zielhost an den lokalen Client weitergereicht. Ein typischer Anwendungsfall ist die Weiterleitung von http/https Ports an den lokalen Client, wenn der Remote-Server keine transparente Verbindung zum Client hat. Im einfachen Fall ist kein Jumpserver zwischen Client und Remote-Host. 

![Einfaches local port forwarding](../images/ssh-localportforwarding1.png)

* -L: local portforwarding
* client: kann weggelassen werden
* port-client: ist ein freier Port auf dem Client
* zielhost: Adresse des Zielhosts (sollte die Anwendung nur auf localhost den Service bereitstellen, wird hier *localhost* angegeben)
* port-app: Port auf dem die Applikation lauscht (bspw. 443) 
* username@zielhost: die Anmeldung am Zielhost

Mit den ssh Optionen
* -f wird ssh im Background ausgeführt
* -N wird keine interaktive Shell geöffnet
Damit kann der local Forwardprozess auch in den Hintergrund der aktiven Terminalsession gelegt werden und es kann mit dieser weiter gearbeitet werden.

Ein Webservice wird auf dem Client dann über die Browser-URL http(s)//localhost:\<port-client\> aufgerufen.


# Local Portforwarding von Pods in einem Kubernetescluster

In diesem Beispiel läuft in einem Kubernetescluster eine Anwendung, die diese auf Port 8200 bereitstellt. Die Verbindung ist wie folgt

Client (C) -> Jumpserver (J) -> Kubernetes - Adminserver (K) -> Hashicorp Vault Service

Auf dem Server K wird der Port 8200 geforwarded. 

```
kubectl get svc -A 
kubectl port-forward -n vault service/vault 8200:8200 
```

Der Port wird dann via ssh Jumpserver auf den Client geforwarded: 

```
ssh -L 8200:localhost:8200 -J <user>@<jumphost> <user>@<kubernets adminserver>
```

Via lokalem Browser dann https://localhost:8200 die Anwendung aufrufen. 








