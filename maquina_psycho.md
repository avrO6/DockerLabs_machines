#Maquina Psycho
----

Desplegamos la maquina Psycho de dockerlabs.

![image](https://github.com/user-attachments/assets/58dea31b-2267-4e68-8eb7-27fac506dfad)


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

![image](https://github.com/user-attachments/assets/f9f3886c-4adb-4e58-952c-1d3cac53c1af)


Tenemos abiertos los puertos 22 y 80. Vamos a hacer un escaneo de versiones de los servicios que corren en esos puertos.

```bash
nmap -p22,80 -sCV 172.17.0.2 -oN targeted.txt
```

- `-p22,80`: Indicamos que el escaneo se haga solo sobre esos 2 puertos.
- `-sCV`: Indicamos que queremos hacer un escaneo de las versiones que tienen los servicios corriendo en esos puertos y lanza unos scripts de reconocimiento que nos daran un poco mas de información sobre el servicio.

![image](https://github.com/user-attachments/assets/3a31f604-dfc1-48d9-9fbf-61358a01b4a8)

El escaneo nos muestra las versiones de los servicios que corren por los puertos y podemos ver que tenemos el servicio ssh, pero no disponemos de credenciales y un servicio web que podemos ver que tiene.

![image](https://github.com/user-attachments/assets/19c20430-5549-4dbf-a684-69451d14e1a5)
![image](https://github.com/user-attachments/assets/a47861fc-6889-4dd0-944f-aa7a69e55aa2)

En la web no parece haber nada excepto un mensage de error extraño que aparece justo debajo del footer. Si miramos el codigo fuente vemos el mismo mensaje de error el final peor no hay nada interesante tampoco.
Como no veo nada en la web vamos ha hacer un poco de fuzzing de directorios con gobuster a ver si encontramos algo.

```bash
gobuster dir --url 'http://172.17.0.2' -w /usr/share/wordlists/seclists/Discovery/Web-Content/common.txt
```

![image](https://github.com/user-attachments/assets/f51112a4-305a-47be-aeca-2a961167b937)

Vemos un assets y un index.php. El index es el archivo principal, donde nos hemos metido de primeras. Asique vamos a mirar el direcctorio /assets.
Aqui nos encontramos una imagen que podemos descargarla y mirar los metadatos por si tuviera algo que nos sirva, como un nombre de usuario para probar en el servicio ssh.

![image](https://github.com/user-attachments/assets/7e2a5db9-f2c7-4b3c-b3e9-6c273ea59599)


```bash
exiftool background.jpg 
```

![image](https://github.com/user-attachments/assets/64f8078e-2d45-47d2-8d36-48794c3562fc)

Tampoco. No vemos ninguna información relevante.


