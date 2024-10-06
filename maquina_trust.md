#Maquina Trust
----

Desplegamos la maquina trust de dockerlabs

![image](https://github.com/user-attachments/assets/50d88287-2321-40c7-b0cc-12db16c0bb50)

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

![image](https://github.com/user-attachments/assets/6824378c-293f-4ac7-b775-8616f6bc28b4)

Tenemos abiertos los puertos 22 y 80. Vamos a hacer un escaneo de versiones de los servicios que corren en esos puertos.

```bash
nmap -p22,80 -sV 10.89.0.2
```

- `-p22,80`: Indicamos que el escaneo se haga solo sobre esos 2 puertos.
- `-sV`: Indicamos que queremos hacer un escaneo de las versiones que tienen los servicios corriendo en esos puertos.

![image](https://github.com/user-attachments/assets/86d3bf98-8432-4ecd-871c-45f4571c3b3b)

Podemos ver las versiones de los servicios SSH y http lo que nos da información sobre el sistema operativo.
Vamos a ver lo que tenemos en la web. 

![image](https://github.com/user-attachments/assets/7776f2d9-6fb4-48b0-9147-df915ebecb8b)

Tenemos la pagina por defecto de apache por lo que haremos un poco de fizzing con gobuster para ver si tenemos algun directorio mas que podamos ver.

```bash
gobuster dir -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php,html,txt -t 20 -u http://10.89.0.2
```

-`dir`: Usamos el modo de enumeracion de directorios.
-`-w`: Lo usamos para indicar la ruta de la wordlist que usaremos.
-`-x`: Indicamos las extesiones.
-`-t`: Indicamos los hilos que queremos usar para el fuzzing.
-`-u`: Lo usamos para indicar la url a la que se dirige el ataque.

![image](https://github.com/user-attachments/assets/11007117-937f-449c-b17c-3dd9db6a0b03)

Tenemos un directorio llamado secret.php. Le echamos un vistazo.

![image](https://github.com/user-attachments/assets/54762586-6bc1-4cd8-9b65-0a4fb773bd24)

Podemos ver un mensaje dirigido hacia Mario, por lo que podemos pensar que hay algun usuario llamado mario. El único vector de ataque que se me ocurre es hacer fuerza bruta al otro puerto abierto, el 22 que es el ssh con el usuario mario.

```bash
hydra -l mario -P /usr/share/worlists/rockyou.txt ssh://10.89.0.2
```

![image](https://github.com/user-attachments/assets/cf90f859-0b45-4508-b78e-838b6cf22992)

Encontramos credenciales para acceder por ssh -> mario:chocolate

```bash
ssh mario@10.89.0.2
```
Y estamos dentro.
