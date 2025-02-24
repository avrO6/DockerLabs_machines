#Maquina Psycho
----

Desplegamos la maquina Psycho de dockerlabs.

-.


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

-.


Tenemos abiertos los puertos 22 y 80. Vamos a hacer un escaneo de versiones de los servicios que corren en esos puertos.

```bash
nmap -p22,80 -sCV 172.17.0.2 -oN targeted.txt
```

- `-p22,80`: Indicamos que el escaneo se haga solo sobre esos 2 puertos.
- `-sCV`: Indicamos que queremos hacer un escaneo de las versiones que tienen los servicios corriendo en esos puertos y lanza unos scripts de reconocimiento que nos daran un poco mas de información sobre el servicio.

-.

El escaneo nos muestra las versiones de los servicios que corren por los puertos y podemos ver que tenemos .