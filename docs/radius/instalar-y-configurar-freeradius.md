# Instalar y configurar FreeRadius

## Instalación

### Ubuntu

#### Añadir el repositorio a APT

Dependiendo de la versión de Ubuntu, añadiremos la línea indicada al fichero `/etc/apt/sources.list`:

##### Trusty Tahr (14.04)

```
deb http://packages.networkradius.com/releases/ubuntu-trusty trusty main
```

##### Xenial Xerus (16.04)

```
deb http://packages.networkradius.com/releases/ubuntu-xenial xenial main
```

##### Bionic Beaver (18.04)

```
deb http://packages.networkradius.com/releases/ubuntu-bionic bionic main
```

#### Añadir clave de autenticación de paquetes

Los paquetes están firmados con la siguiente clave [PGP](http://packages.networkradius.com/pgp/packages@networkradius.com). Para añadirla a nuestro sistema ejecutamos los siguientes comandos:

```bash
sudo apt-key adv --keyserver keys.gnupg.net --recv-key 0x41382202
sudo apt-get update
```

#### Instalar FreeRadius

```bash
sudo apt install freeradius
```

### Debian

#### Añadir el repositorio a APT

Dependiendo de la versión de Debian, añadiremos la línea indicada al fichero `/etc/apt/sources.list`:

##### Debian 8 “Jessie”

```
deb http://packages.networkradius.com/releases/debian-jessie jessie main
```

##### Debian 9 “Stretch”

```
deb http://packages.networkradius.com/releases/debian-stretch stretch main
```

#### Añadir clave de autenticación de paquetes

Los paquetes están firmados con la siguiente clave [PGP](http://packages.networkradius.com/pgp/packages@networkradius.com). Para añadirla a nuestro sistema ejecutamos los siguientes comandos:

```bash
sudo apt-key adv --keyserver keys.gnupg.net --recv-key 0x41382202
sudo apt-get update
```

#### Instalar FreeRadius

```bash
sudo apt install freeradius
```

### CentOS

> Nota: CentOS y RedHat enlazan sus librerías LDAP mediante NSS. FreeRADIUS usa OpenSSL. NSS y OpenSSL no pueden usarse desde la misma aplicación porque fallaría. FreeRADIUS sin embargo debe usar `libldap` de "LDAP Toolbox Project", el cuál usa OpenSSL.

Importar la clave publica de "LDAP Toolbox Project":

```bash
rpm --import https://ltb-project.org/lib/RPM-GPG-KEY-LTB-project
```

Crear un fichero de repositorio Yum/DNF para "LDAP Toolbox Project":

```bash
echo '[ltb-project]
name=LTB project packages
baseurl=https://ltb-project.org/rpm/$releasever/$basearch
enabled=1
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-LTB-project' > /etc/yum.repos.d/ltb-project.repo
```

Importar la clave [PGP](http://packages.networkradius.com/pgp/packages@networkradius.com) para NetworkRADIUS:

```bash
gpg --keyserver keys.gnupg.net --recv-key 0x41382202
gpg --armor --export packages@networkradius.com > /etc/pki/rpm-gpg/packages.networkradius.com.gpg
```

Crear un fichero de repositorio Yum/DNF para NetworkRADIUS:

```bash
echo '[networkradius]
name=NetworkRADIUS-$releasever
baseurl=http://packages.networkradius.com/releases/centos/$releasever/repo/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/packages.networkradius.com.gpg' > /etc/yum.repos.d/networkradius.repo
```

### Instalar FreeRadius