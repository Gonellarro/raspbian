# Instal·lació de raspbian complet

## Inicial

1. Gravar imatge

2. Entrar amb l'usuari pi/raspberry

3. Executar:

   ```bash
   sudo raspi-config
   ```

   

4. Habilitar ssh: *3 (Interface Options)/I2 (SSH)/Sí*

5. Canviar el teclat: *5 (Localisation Options)*

   1. Locale/
      1. es_ES ISO-8859-1
      2. es_ES.UTF-8 UTF-8
   2. Keyboard
      1. PC genérico 105 teclas (int)
      2. Espanol
      3. Distribución por omisión del teclado
      4. Sin tecla modificadora

6. Actualitzar: *8 (update)*

## Instal·lar samba i configurar unitat compartida

1. ```bash
   sudo apt-get install samba samba-common-bin
   ```

   

2. Crear la carpeta compartida i donar-li permisos de control total a tothom (si ho feim per casa): 

   ```bash
   mkdir /home/pi/shared
   chmod 777 shared 
   ```

   

3. Configurar el share per a què sigui accessible: 

   ```bash
   sudo nano /etc/samba/smb.conf
   ```

   

4. Afegir el següent al final del fitxer:

   ```bash
   [PiShared]
   path = /home/pi/shared
   writeable=Yes
   create mask=0777
   directory mask=0777
   public=yes
   ```

   

5. Reiniciar el servei de samba: 

   ```bash
   sudo systemctl restart smbd
   ```

   

Font: https://pimylifeup.com/raspberry-pi-samba/



## Instal·lar docker i docker compose a Raspberry pi

1. ```bash
   curl -sSL https://get.docker.com | sh
   ```

2. Un cop finalitzat l'script, afegir els permisos a l'usuari actual (pi) per executar les ordres de Docker.

   ```bash
   sudo usermod -aG docker ${USER}
   ```

3. Revisar que s'ha instal·lat correctament.

   ```bash
   sudo docker version
   sudo docker info
   ```

4. Executar el contenidor "Hello World"

   ```bash
   docker run hello-world
   ```

5. Instal·lam docker compose. Docker-Compose normalment s'instal·la mitjançant pip3. Per això, hem de tenir instal·lats python3 i pip3. Si no el tenim instal·lat, hem d'executar les ordres següents:

   ```bash
   sudo apt-get install libffi-dev libssl-dev
   sudo apt install python3-dev
   sudo apt-get install -y python3 python3-pip
   ```

6. Un cop instal·lats python3 i pip3, podem instal·lar Docker-Compose mitjançant l'ordre següent:

   ```bash
   sudo pip3 install docker-compose
   ```

7. Habilitar docker compose per a que arranqui els contenidors en iniciar

   ```bash
   sudo systemctl enable docker
   ```

   Amb aquesta característica, els contenidors amb una política de reinici establerta com sempre o si no s'aturaran es reiniciaran automàticament després d'un reinici.
   
   Fonts: 

https://pumpingco.de/blog/setup-your-raspberry-pi-for-docker-and-docker-compose/

https://phoenixnap.com/kb/docker-on-raspberry-pi

## Instal·lació de AdGuard Home en docker

1. Instal·larem AdGuard en una carpeta anomenada adguard. Recomano posar-la dins la carpeta compartida (shared), per poder accedir des de qualsevol ordinador

   ```bash
   mkdir adguard
   ```

2. Dins la carpeta, cream el fitxer de docker-compose:

   ```bash
   nano docker-compose.yml
   ```

   ```yaml
   version: "3.3"
   services:
     adguardhome:
       image: adguard/adguardhome
       container_name: adguardhome
       ports:
         - 3000:3000/tcp
         - 8082:80/tcp
         - 53:53/tcp
       volumes:
         - ./work:/opt/adguardhome/work
         - ./conf:/opt/adguardhome/conf
       restart: unless-stopped
   ```

   Guardam amb Ctrl-X, deim Y

3. Engegam docker-compose:

   ```bash
   sudo docker-compose up -d
   ```

4. Anam amb un navegador a la direcció de la rasp:3000
   ![/2021/08/setting-up-adguard-home/adguard-initial.jpg](https://blog.thatopsguy.com/2021/08/setting-up-adguard-home/adguard-initial.jpg)

5. En el moment en que ens demana quin port hem de fer servir, canviar el 80 pel 8082
   ![/2021/08/setting-up-adguard-home/adguard1.png](https://blog.thatopsguy.com/2021/08/setting-up-adguard-home/adguard1.png)

6. Introduir l'usuari/contrasenya
   ![/2021/08/setting-up-adguard-home/adguard-create-admin-user.png](https://blog.thatopsguy.com/2021/08/setting-up-adguard-home/adguard-create-admin-user.png)

7. I ja ha acabat la configuració al AdGuard

8. Canviar al router el DNS, posant-hi la IP de la raspberry

9. Per accedir al dashboard d'AdGuard, s'hi accedeix amb un navegador a la IP de la rasp:8082
   ![img](https://cdn.adguard.com/public/Adguard/Blog/AGHome/dashboard.jpg)



