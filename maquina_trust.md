
Desplegamos la maquina trust de dockerlabs

![[Pasted image 20241006185608.png]]

---- 
## Escaneo 

Primero hacemos un escaneo de todos los puertos para ver los que están abiertos.

```bash
nmap -p- --open -T5 -n 10.89.0.2 -vvv -oN pertos.txt
```

- `-p-`: Indicamos que vas a escanear todo el rango de puertos
- `--open`: Indicamos que te muestre solo los puertos que estén abiertos.
- `-T5`: Aplicamos la plantilla de escaneo mas rápida que tiene nmap. 
- `-n`: Quitamos la resolución DNS del escaneo.
- `-vvv`: Añadimos verbose.
- `-oN`: Indicamos que nos saque un output del escaneo en formato nmap al archivo **puertos.txt**.

![[Pasted image 20241006190618.png]]

Tenemos abiertos los puertos 22 y 80. Vamos a hacer un escaneo de versiones de los servicios que corren en esos puertos.

```bash
nmap -p22,80 -sV 10.89.0.2
```

- `-p22,80`: Indicamos que el escaneo se haga solo sobre esos 2 puertos.
- `-sV`: Indicamos que queremos hacer un escaneo de las versiones que tienen los servicios corriendo en esos puertos.

![[Pasted image 20241006191644.png]]

Podemos ver las versiones de los servicios SSH y http lo que nos da información sobre el sistema operativo.
