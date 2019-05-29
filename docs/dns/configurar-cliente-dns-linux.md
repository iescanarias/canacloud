# Configurar cliente de DNS en GNU/Linux

Este proceso varia dependiendo del sistema operativo, aunque para la mayoría de las distribuciones GNU/Linux consiste en añadir el servidor DNS al fichero  `/etc/resolv.conf`.

## Clientes Debian

En Debian y Ubuntu podemos editar el fichero `head`, el cuál se añade al principio del fichero `resolv.conf` durante el arranque:

```bash
sudo nano /etc/resolvconf/resolv.conf.d/head
```

Debemos añadir las siguientes líneas a este fichero:

```bash
search iesdomingoperezminik.es  	# nuestro dominio privado
nameserver 192.168.0.200  			# dirección IP de nuestro servidor
```

Guardamos y salimos.

A continuación ejecutamos el siguiente para generar un nuevo fichero `resolv.conf`:

```bash
sudo resolvconf -u
```

Y ya estaría configurado el cliente para usar el servidor DNS indicado.

## Clientes RedHat

En RedHat, CentOS y Fedora, simplemente hay que editar el fichero `resolv.conf`:

```bash
sudo nano /etc/resolv.conf
```

Luego añadimos las siguientes líneas al comienzo del fichero:

```bash
search iesdomingoperezminik.es  	# nuestro dominio privado
nameserver 192.168.0.200  			# dirección IP de nuestro servidor
```

Guardamos y salimos. 

Con esto el cliente ya está configurado para usar el servidor DNS indicado.

## Verificar los clientes

Podemos usar `nslookup` para comprobar si el cliente consulta el servidor de nombres indicado.

### Búsqueda directa

Por ejemplo, podemos realizar una búsqueda directa para obtener la dirección IP de *host1.iesdomingoperezminik.es* ejecutando el siguiente comando:

```bash
nslookup host1
```

Al preguntar por *host1* se expande a *host1.iesdomingoperezminik.es* porque en la opción `search` indicamos como nombre de dominio de búsqueda *iesdomingoperezminik.es*. 

La salida del comando sería como la siguiente:

```bash
Output:
Server:     192.168.0.250
Address:    192.168.0.250#53

Name:   host1.iesdomingoperezminik.es
Address: 192.168.0.1
```

### Búsqueda inversa

Para comprobar la búsqueda inversa hacemos la consulta indicando la dirección IP del host:

```
nslookup 192.168.0.1
```

Y la salida debería ser como la siguiente:

```bash
Output:
Server:     192.168.0.250
Address:    192.168.0.250#53

1.0.168.192.in-addr.arpa   name = host1.iesdomingoperezminik.es.
```

