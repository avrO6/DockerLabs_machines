#Maquina Injection
----

Desplegamos la maquina Injection de dockerlabs
![image](https://github.com/user-attachments/assets/e7778456-d009-4e5a-a50d-9209bb5f6c37)

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
- `-oN`: Indicamos que nos saque un output del escaneo en formato nmap al archivo puertos.txt.

![image](https://github.com/user-attachments/assets/232b7fad-639a-4001-a8d5-77ad906378c4)

Tenemos abiertos los puertos 22 y 80. Vamos a hacer un escaneo de versiones de los servicios que corren en esos puertos.

```bash
nmap -p22,80 -sV 10.89.0.2 -oN puertos_versiones.txt
```

- `-p22,80`: Indicamos que el escaneo se haga solo sobre esos 2 puertos.
- `-sV`: Indicamos que queremos hacer un escaneo de las versiones que tienen los servicios corriendo en esos puertos.

![image](https://github.com/user-attachments/assets/8f579af8-b00f-46f1-9092-ad3cedee0ba9)

----

## Intrusion
Tenemos una web en la que hay un login, asique probamos a hacer una inyeccion sql en el formulario.

![image](https://github.com/user-attachments/assets/d7856905-0757-4cbb-8ccb-7c14148d2de6)

Hacemos login y vemos que nos deja entrar con el usuario Dylan y nos pone que se ha introducido correctamente la contraseña siguiente.

![image](https://github.com/user-attachments/assets/dd4ab3d9-39e1-484d-8564-3f0bddc6dc6e)

Teniendo esta información puedo pensar que dylan es un usuario del sistema y que a traves de SSH me podria conectar con esa contraseña.

Vamos a probar.

```bash
ssh dylan@10.88.0.2 
```

Probamos con la contraseña que hemos encontrado en la web y nos acabamos de conectar como el usuario dylan.

![image](https://github.com/user-attachments/assets/ac1ca330-304a-42e7-9fc1-f09e3c145e0b)

----

## Escalada de privilegios

EN PROCESO...



