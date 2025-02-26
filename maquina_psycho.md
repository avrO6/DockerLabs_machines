#Maquina Psycho
----

Desplegamos la maquina Psycho de dockerlabs.

![image](https://github.com/user-attachments/assets/a4eda7fc-b602-4ea1-b93a-d7e0a4fce028)


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

![image](https://github.com/user-attachments/assets/6f0c4882-85f1-4210-b5da-a46829048aab)


Tenemos abiertos los puertos 22 y 80. Vamos a hacer un escaneo de versiones de los servicios que corren en esos puertos.

```bash
nmap -p22,80 -sCV 172.17.0.2 -oN targeted.txt
```

- `-p22,80`: Indicamos que el escaneo se haga solo sobre esos 2 puertos.
- `-sCV`: Indicamos que queremos hacer un escaneo de las versiones que tienen los servicios corriendo en esos puertos y lanza unos scripts de reconocimiento que nos daran un poco mas de información sobre el servicio.

![image](https://github.com/user-attachments/assets/c13d0fb9-2adb-41d2-9686-823c16f82bdd)

El escaneo nos muestra las versiones de los servicios que corren por los puertos y podemos ver que tenemos el servicio ssh, pero no disponemos de credenciales y un servicio web que podemos ver que tiene.

![image](https://github.com/user-attachments/assets/ec072f64-b42f-46e4-90d5-fbb482591784)
![image](https://github.com/user-attachments/assets/e375c0d0-7ef8-40ff-8e94-3c33c21f5bb4)

En la web no parece haber nada excepto un mensage de error que aparece justo debajo del footer. Si miramos el codigo fuente vemos el mismo mensaje de error el final peor no hay nada interesante tampoco.
Como no veo nada en la web vamos ha hacer un poco de fuzzing de directorios con gobuster a ver si encontramos algo.

```bash
gobuster dir --url 'http://172.17.0.2' -w /usr/share/wordlists/seclists/Discovery/Web-Content/common.txt
```

![image](https://github.com/user-attachments/assets/07492d04-8738-4d76-ae7a-13feec7928af)

Vemos un assets y un index.php. El index es el archivo principal, donde nos hemos metido de primeras. Asique vamos a mirar el direcctorio /assets.
Aqui nos encontramos una imagen que podemos descargarla y mirar los metadatos por si tuviera algo que nos sirva, como un nombre de usuario para probar en el servicio ssh.

![image](https://github.com/user-attachments/assets/7e2a5db9-f2c7-4b3c-b3e9-6c273ea59599)


```bash
exiftool background.jpg 
```

![image](https://github.com/user-attachments/assets/8dbcdebd-b4bb-409d-95c8-c9e9f1a03da8)

Tampoco. No vemos ninguna información relevante.

Vamos a ver si ese error en el index es porque el php que corre por detras esta intentando llamar a un recurso incorrecto y a traves de algun parametro en la url podemos explotar un LFI.

```bash
wfuzz -w /usr/share/wordlists/seclists/Discovery/Web-Content/common.txt -u 'http://172.17.0.2/index.php?FUZZ=/etc/passwd' -c --hl=62
```
![image](https://github.com/user-attachments/assets/1e5aa854-3472-4b6b-a390-8451e2bf4248)

Vemos que el parametro 'secret' es vulnerable a LFI, asique vamos a la web y carbamos el archivo passwd para ver los usuarios del servidor.

![image](https://github.com/user-attachments/assets/649cec1e-3cc8-4d6d-acd3-a08dbb2d15ba)

Tenemos los usuarios 'vaxei' y 'luisillo' y sabiendo que tenemos el puerto 22 corriendo el servicio SSH vamos a intentar conseguir las claves rsa de alguno de ellos.

![image](https://github.com/user-attachments/assets/18166a4a-e320-48af-9d1d-61cbb02d018f)

---- 

## Intrusion

Nos vamos a copiar esa clave rsa en un archivo y le vamos a dar permisos de lectura y escritura para poder usarlo como clave de la conexión.

```bash
chmod 600 id_rsa
ssh -i id_rsa vaxei@172.17.0.2    
```

![image](https://github.com/user-attachments/assets/1dbf79eb-ab38-4501-8d19-0562965c8c2d)


