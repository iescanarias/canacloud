# Instalar y configurar un servidor de DNS en GNU/Linux

En esta guía se explica el proceso para instalar y configurar [BIND](https://es.wikipedia.org/wiki/BIND), un servidor de DNS para sistemas GNU/Linux.

Para ver como configurar los clientes de DNS en GNU/Linux consultar el siguiente [enlace](configurar-cliente-dns-linux).

Para ver como configurar los clientes de DNS en Windows consultar el siguiente [enlace](configurar-cliente-dns-windows).

## Instalación

1. Actualizamos la base de datos de paquetes de APT:

   ```bash
   sudo apt-get update
   ```
2. Instalamos BIND

   ```bash
   sudo apt install bind9 bind9utils bind9-doc
   ```

## Configuración 

### Establecer BIND en modo IPv4

1. Editamos el fichero de configuración del servicio `bind9`:

   ```bash
   sudo nano /etc/default/bind9A
   ```

2. Añadimos "-4" a la variable `OPTIONS`, quedando de la siguiente forma:

   ```properties
   OPTIONS="-4 -u bind"
   ```

3. Guardamos y salimos.

### Configurar el servidor

La configuración de BIND consiste en múltiples ficheros, todos ellos incluidos desde el fichero de configuración principal `named.conf`. El nombre de estos ficheros comienzan con la palabra "named" porque así se llama el servicio que inicia BIND.

#### Opciones

1. Editamos el fichero `named.conf.options`:

   ```bash
   sudo name /etc/bind/named.conf.options
   ```

2. Modificamos el fichero y le añadimos lo siguiente  dentro de `options`:

    ```
    // reenviamos peticiones a los servidores DNS de Google
    forwarders {
    8.8.8.8;
        8.8.4.4;
    };
    ```
    
3. Reiniciamos el servicio para que los cambios surtan efecto:

   ```bash
   sudo service bind9 restart
   ```

4. Finalmente, comprobamos que nuestro servidor es capaz de traducir nombres, de forma que reenvía las peticiones al servidor DNS de Google cuando no sabe traducirlo:

   ```bash
    $ nslookup www.google.com localhost
    Server:         localhost
    Address:        127.0.0.1#53
    
    Non-authoritative answer:
    Name:   www.google.com
    Address: 216.58.211.36
    Name:   www.google.com
    Address: 2a00:1450:4003:80b::2004
    
   ```
   
   > Mediante el comando anterior le estamos pidiendo que resuelva el nombre "www.google.com" contra el servidor DNS "localhost".

#### Zonas de nuestro dominio

1. Editamos el fichero `/etc/bind/named.conf.local`:

   ```bash
   sudo nano /etc/bin/named.conf.local
   ```

3. Añadimos la zona de búsqueda directa (para resolver nombres de dominio en IPs):

   ```
   zone "iesdpm.es" {
       type master;
       file "/etc/bind/zones/db.iesdpm.es";
   };
   ```

5. Y la zona de búsqueda inversa (para resolver IPs en nombres de dominio):

   ```
   zone "0.168.192.in-addr.arpa" {
       type master;
       file "/etc/bind/zones/db.192.168.0";  # red 192.168.0.0/24
   };
   ```

#### Ficheros de zonas de búsqueda

En estos ficheros es donde definimos los registros de DNS. Cuando el servidor reciba una petición de traducción de un nombre en IP o viceversa, buscará en estos ficheros de zona. Si nuestro servidor no gestiona la zona, reenviará la petición a otro servidor, tal y como vimos antes.

##### Crear el fichero de zona de búsqueda directa

Aquí es donde definimos los registros de tipo "A".

1. Creamos el directorio para los ficheros de zona:

   ```bash
   sudo mkdir /etc/bind/zones
   ```

2. Usamos el fichero de ejemplo como base para nuestra zona de búsqueda directa:

   ```bash
	cp /etc/bind/db.local /etc/bind/zones/db.iesdpm.es
	```
	
3. Editamos el fichero de la zona de búsqueda directa:

   ```bash
   sudo nano /etc/bind/zones/db.iesdpm.es
   ```

   Y nos encontramos el siguiente contenido:

   ```
   ;
   ; BIND data file for local loopback interface
   ;
   $TTL    604800
   @       IN      SOA     localhost. root.localhost. (
                                 1         ; Serial
                            604800         ; Refresh
                             86400         ; Retry
                           2419200         ; Expire
                            604800 )       ; Negative Cache TTL
   ;
   @       IN      NS      localhost.
   @       IN      A       127.0.0.1
   @       IN      AAAA    ::1
   ```

6. Editamos el registro SOA, remplazando `localhost` por el FQDN de nuestro dominio. 

   ```
   @       IN      SOA     iesdpm.es. root.iesdpm.es. (
   ```
   
6. Incrementamos el valor de `serial` indicando que la versión ha cambiado (debe hacerse cada vez que se modifique este fichero).

   ```
                                 2         ; Serial
   ```

7. Eliminamos los 3 últimos registros del fichero:

   ```
   @			IN      NS      localhost.
   @			IN      A       127.0.0.1
   @			IN      AAAA    ::1
   ```

7. Añadimos un registro "NS" para nuestro servidor de nombres al final el fichero:

   ```
   ; name servers - NS records
   @			IN		NS		iesdpm.es.
   @			IN		A		192.168.0.250		; IP del servidor DNS
   ```

8. A continuación, añadimos los registros "A" para los hosts de nuestro dominio, tantos como necesitemos:

   ```
   ; 192.168.0.0/24 - A records
   dns    		IN      A		192.168.0.250		
   host1		IN      A		192.168.0.1
   host2		IN      A		192.168.0.2
   [...]
   ```

10. Guardamos y salimos del editor.

##### Crear el fichero de zona de búsqueda inversa

Aquí se definen los registros de tipo "PTR" para las búsquedas inversas.

1. Usamos el fichero de ejemplo como base para nuestra zona de búsqueda inversa:

   ```bash
   cp /etc/bind/db.127 /etc/bind/zones/db.192.168.0
   ```

2. Editamos el fichero copiado antes:

   ```bash
   sudo nano /etc/bind/zones/db.192.168.0
   ```

   Y nos encontramos el siguiente contenido:

   ```
   ;
   ; BIND reverse data file for local loopback interface
   ;
   $TTL    604800
   @       IN      SOA     localhost. root.localhost. (
                                 1         ; Serial
                            604800         ; Refresh
                             86400         ; Retry
                           2419200         ; Expire
                            604800 )       ; Negative Cache TTL
   ;
   @       IN      NS      localhost.      
   1.0.0   IN      PTR     localhost.      
   ```

3. Editamos el registro SOA, remplazando `localhost` por el FQDN de nuestro dominio (*iesdomingoperezminik.es*.; acabado en punto). 

   ```
   @       IN      SOA     iesdpm.es. root.iesdpm.es. (
   ```

4. Incrementamos el valor de `serial` indicando que la versión ha cambiado (debe hacerse cada vez que se modifique este fichero).

   ```
                                 2         ; Serial
   ```

5. Eliminamos los 2 últimos registros del fichero:

   ```
   @			IN      NS      localhost.      
   1.0.0		IN      PTR     localhost.   
   ```

6. Añadimos un registro "NS" para nuestro servidor de nombres al final el fichero:

   ```
   ; name servers - NS records
   @			IN      NS      iesdpm.es.
   ```

7. A continuación, añadimos los registros "PTR" para los hosts de nuestro dominio, tantos como necesitemos:

   ```
   ; 192.168.0.0/24 - PTR records
   250   		IN      PTR     dns.iesdpm.es.
   1   		IN      PTR     host1.iesdpm.es.
   2   		IN      PTR     host2.iesdpm.es.
   [...]
   ```

8. Guardamos y salimos del editor.

### Comprobar la configuración de Bind

Ejecutamos el siguiente comando para comprobar si la sintaxis de los ficheros de configuración es correcta:

```bash
sudo named-checkconf
```

Si los ficheros de configuración están correctos, terminará sin decir nada. Si encuentra algún problema, debemos revisar de nuevo los fichero de configuración y volver a ejecutar el comando anterior.

Para comprobar también los ficheros de zonas de búsqueda, usamos la opción `-z`:

```bash
sudo named-checkconf -z
```

Obteniendo algo parecido a lo siguiente en caso de que todo esté bien:

```bash
zone iesdpm.es/IN: loaded serial 3
zone 0.168.192.in-addr.arpa/IN: loaded serial 2
zone localhost/IN: loaded serial 2
zone 127.in-addr.arpa/IN: loaded serial 1
zone 0.in-addr.arpa/IN: loaded serial 1
zone 255.in-addr.arpa/IN: loaded serial 1
```

Una vez hemos comprobado que los ficheros están correctos, podemos reiniciar el servicio BIND.

### Reiniciar el servicio

Para reiniciar BIND:

```bash
sudo service bind9 restart
```

Con esto ya estaría funcionando nuestro servidor DNS.

## Referencias

* [How To Configure BIND as a Private Network DNS Server on Ubuntu 14.04](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-ubuntu-14-04)