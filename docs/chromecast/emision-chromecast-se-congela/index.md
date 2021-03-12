# La emisión de Chromecast se congela tras unos segundos

En ocasiones, al proyectar en un dispositivo Chromecast (ya sea built-in o un dispositivo original), tras unos pocos segundos, la emisión de congela, aunque el audio sigue funcionando.

Un posible solución a este problema es cambiar la **unidad máxima de transferencia (MTU)** del sistema a un valor ligeramente inferior a 1500.

## Cambiar el tamaño de MTU en Windows 10

Para cambiar el tamaño de MTU en Windows podemos seguir los siguientes pasos:

1. Abrir **CMD** (Símbolo del sistema) como Administrador

2. El siguiente comando muestra el valor actual de MTU de todas y cada una de las interfaces de red del sistema:

   ```bash
   netsh interface ipv4 show subinterface
   ```

   El resultado sería algo similar a lo siguiente:

   ```bash
   C:\Windows\system32>netsh interface ipv4 show subinterface
   
          MTU  MediaSenseState    Bytes ent.  Bytes sal. Interfaz
   ----------  ---------------  -----------  ----------  -------------
   4294967295                1            0     3977096  Loopback Pseudo-Interface 1
         1500                1  28810442487  3955475351  Wi-Fi
         1500                5            0           0  Conexión de área local* 1
         1500                2            0       97425  Conexión de área local* 10
         1500                5            0           0  Conexión de red Bluetooth
         1500                1            0     8497213  VirtualBox Host-Only Network
         1500                1            0      119556  Npcap Loopback Adapter
   ```

   > En mi caso, quiero cambiar el tamaño MTU de la interfaz de red de nombre "Wi-Fi"

3. Mediante el siguiente comando cambiamos el tamaño MTU de la interfaz de red indicada a 1458 (por ejemplo):

   ```bash
   netsh interface ipv4 set subinterface “Wi-Fi” mtu=1458 store=persistent
   ```

   > Recuerda cambiar "Wi-Fi" por el nombre de tu interfaz de red.

   > En algún caso el problema podría resolverse incrementando el valor de MTU, aunque nunca deberá ser inferior a 1400 ni superior a 15500.

4. Por último, reiniciamos el PC.

¡Y listo, a chromecastear!

## Referencias

- [Chromecast stops casting after a few seconds](https://cybermaterial.com/chromecast-stops-casting-after-a-few-seconds/)