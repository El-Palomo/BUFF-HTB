# BUFF-HTB

Desarrollo de la VM BUFF de HACK THE BOX (HTB)

## 1. Configuración de la VM

- La VM se encuentra en estado de retirada de HTB
- Se debe activar la VM para poder usarla, se requiere una suscripción PREMIUM.

## 2. Escaneo de Puertos

```
┌──(root㉿kali)-[~/HT/BUFF]
└─# cat full.nmap 
# Nmap 7.92 scan initiated Tue Apr 19 19:05:35 2022 as: nmap -n -P0 --top-ports 5000 -sS -sC -vv -oA full 10.129.25.107
Nmap scan report for 10.129.25.107
Host is up, received user-set (0.26s latency).
Scanned at 2022-04-19 19:05:37 EDT for 104s
Not shown: 4999 filtered tcp ports (no-response)
PORT     STATE SERVICE    REASON
8080/tcp open  http-proxy syn-ack ttl 127
| http-open-proxy: Potentially OPEN proxy.
|_Methods supported:CONNECTION
|_http-title: mrb3n's Bro Hut
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS

Read data files from: /usr/bin/../share/nmap
# Nmap done at Tue Apr 19 19:07:21 2022 -- 1 IP address (1 host up) scanned in 106.96 seconds
```

<img src="https://github.com/El-Palomo/BUFF-HTB/blob/main/Buff1.jpg" width=80% />
