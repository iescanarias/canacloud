# Instalación de Microsoft SQL Server Management Studio

Proceso de instalación de la herramienta MS SQL Server Management Studio en Windows.

> Para conectar de forma remota al servidor SQL Server consultar el siguiente [enlace](acceso-remoto-sqlserver).

## Windows

### Mediante Chocolatey

1. Instalar el gestor de paquetes [Chocolatey](https://chocolatey.org/install).

2. Abrir un terminal (PowerShell o Símbolo del sistema) como Administrador.

3. Ejecutar el siguiente comando:

   ```bash
   choco install -y sql-server-management-studio
   ```

### Mediante el instalador

1. Descargar el instalador desde el siguiente [enlace](https://go.microsoft.com/fwlink/?linkid=2043154) y ejecutarlo.
2. 

## GNU/Linux

Abrir un terminal y ejecutar los siguientes comandos:

1. Importar la clave pública GPG del repositorio:

```bash
curl -s https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add -
```

2. Añadir el repositorio de paquetes:

```bash
sudo add-apt-repository "$(wget -qO- https://packages.microsoft.com/config/ubuntu/18.04/prod.list)"
```

3. Actualizamos la lista de paquetes con la información del nuevo repositorio:

```bash
sudo apt-get update
```

4. Instalamos SQL Server:

```bash
sudo apt-get install mssql-tools
```

5. Ejecutamos el script de configuración:

```bash
sudo /opt/mssql/bin/mssql-conf setup
```
