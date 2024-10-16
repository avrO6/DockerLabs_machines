#Maquina Injection
Desplegamos la maquina Injection de dockerlabs
![image](https://github.com/user-attachments/assets/e7778456-d009-4e5a-a50d-9209bb5f6c37)

Escaneo
Primero hacemos un escaneo de todos los puertos para ver los que están abiertos.

nmap -p- --open -T5 -n 10.89.0.2 -vvv -oN pertos.txt
-p-: Indicamos que vas a escanear todo el rango de puertos
--open: Indicamos que te muestre solo los puertos que estén abiertos.
-T5: Aplicamos la plantilla de escaneo mas rápida que tiene nmap.
-n: Quitamos la resolución DNS del escaneo.
-vvv: Añadimos verbose.
-oN: Indicamos que nos saque un output del escaneo en formato nmap al archivo puertos.txt.
