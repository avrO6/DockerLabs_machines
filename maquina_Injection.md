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

Ejecutamos `sudo -l` para listar los permisos sudo del usuario actual. 
Pero como vemos nos da error.

![image](https://github.com/user-attachments/assets/beef7f85-f02c-481c-96e1-72a363675ec0)

Para poder ver si hay algun archivo que podamos ejecutar con permisos de sudo, ejecutamos el comando `finf / -perm -4000 2>/dev/null` que realiza una busqueda desde la raiz del sistema de archivos que tengan el bit SUID activado.

- `find /`: Inicia la búsqueda desde el directorio raíz (/), lo que significa que revisa todo el sistema de archivos.

- `-perm -4000`: Busca archivos que tengan el bit de SUID activado. Este bit permite que un archivo se ejecute con los permisos del propietario del archivo en lugar de los permisos del usuario que lo ejecuta. El 4000 representa este bit específico.

- `2>/dev/null`: Redirige la salida de error (cualquier mensaje de error) a /dev/null, lo que significa que se descartarán esos mensajes. Esto se utiliza para evitar que aparezcan errores en la pantalla, como los que pueden surgir al intentar acceder a directorios sin permisos.

![image](https://github.com/user-attachments/assets/4dca1198-6eef-40ce-93c5-eda20ea16b96)

Podemos utilizar **GTFObins** para ver información sobre binarios de Unix que pueden ser utilizados para explotar vulnerabilidades en sistemas.

Y vemos que podemos utilizar el binario env para esacalar privilegios por SUID.

![image](https://github.com/user-attachments/assets/8e6bbf84-9ae0-4edf-8a2c-9c40748e502e)

Nos indica que podemos utilizar `./env /bin/sh -p` para escalar los privilegios.

- `./env`: Este es el binario env, que se utiliza para ejecutar un comando en un entorno modificado. El prefijo ./ indica que se está ejecutando env en el directorio actual.

- `/bin/sh`: Este es el comando que se ejecutará, en este caso, una nueva instancia de sh.

- `-p`: Esta opción se utiliza para iniciar la shell sin desactivar los privilegios efectivos del usuario, lo que significa que se conservarán los privilegios de usuario elevando el acceso a la shell.

Vamos a usar el comando `which env` para ver en que directorio se encuentra nuestro binario y como no estamos en ese directorio ejecutamos el comando `/usr/bin/env /bin/bash -p`.

En mi caso pongo `/bin/bash` porque prefiero una bash a una sh, pero el funcionamiento del comando es el mismo.

![image](https://github.com/user-attachments/assets/ebac8e13-bfd2-4fce-a491-052c387b2e92)

Ya somos usuario root. Maquina COMPLETADA.



