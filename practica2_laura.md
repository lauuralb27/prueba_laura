# Práctica 2 

## Preparación en instalación de la red
**Cliente Windows:**
Vamos a instalar una máquina virtual con el sistema operativo Windows. Esta máquina tendrá un adaptador de red configurado para funcionar en red interna, a la que llamaremos SRI200. El nombre que daremos a esta máquina será W-LAURA.

![Máquina de Windows](./imagenes-sri/maquina-windows.png)

Configuramos el cliente de Windows para que obtenga su dirección IP de forma automática a través de un servidor DHCP. 
![Máquina de Windows](./imagenes-sri/ip-cliente-windows-dhcp.png)
 
**Router Pfsese:**

En el siguiente paso, vamos a instalar el router PfSense, que será el encargado de gestionar la red. Este router tendrá dos adaptadores de red: el primero estará en modo adaptador puente para conectarse a la red externa y permitir el acceso a Internet, y el segundo en red interna SRI200 para comunicar con las máquinas virtuales.

![Máquina de PfSense](./imagenes-sri/maquina-pfsense.png)

Asignamos la dirección IP 10.0.200.1 al router, que será la puerta de enlace de nuestra red interna. Además, desactivamos el servidor DHCP en esta interfaz para evitar conflictos con el servidor DHCP que configuraremos más adelante en otra máquina.

![Máquina de PfSense](./imagenes-sri/ip-pfsense.png)

**Cliente Debian:**

Instalamos una máquina virtual con el sistema operativo Debian Server para simular un cliente Linux. A esta máquina le asignaremos un único adaptador de red en red interna SRI200, y la llamaremos U-LAURA.

![Máquina de Debian_cliente](./imagenes-sri/maquina_debian_cliente.png)

Configuramos esta máquina para que obtenga su dirección IP mediante DHCP. Para lograr esto, editamos el archivo /etc/network/interfaces, donde especificamos que la interfaz de red debe configurarse automáticamente.es

![Máquina de Debian_cliente](./imagenes-sri/ip-cliente-linux.png)

**Servidor Debian:**

Ahora instalamos otra máquina virtual con el sistema operativo Debian, que funcionará como servidor en nuestra red interna. Esta máquina tendrá un adaptador de red configurado en red interna SRI200 y se llamará D-LAURA.

![Máquina de Debian](./imagenes-sri/maquina-debian.png)

 Al servidor se le asigna la dirección IP 10.0.200.2, configurando también la puerta de enlace predeterminada como 10.0.200.1 (la dirección IP del router) y usando el DNS del instituto. La configuración de red se realiza en el archivo /etc/network/interfaces.

![Máquina de WinDebiandows](./imagenes-sri/ip-debian.png)

![Máquina de WinDebiandows](./imagenes-sri/ip-a-debian.png)

Para verificar si hay conectividad, hacemos un ping al sitio web www.google.es. Esto nos confirma si la máquina puede navegar por Internet.

![Navega por internet](./imagenes-sri/ping-google-debian.png)

## Ejercicio 1

**Servidor Debian:**

En este paso, instalamos el paquete isc-dhcp-server en la máquina Debian que actúa como servidor. Utilizamos el siguiente comando para llevar a cabo la instalación:

![Servidor Debian](./imagenes-sri/apt-get-install-debian.png)

Después de la instalación, ejecutamos el comando ss -ltun para obtener información detallada sobre las conexiones de red activas.

![Servidor Debian](./imagenes-sri/ss_-ltun_debian.PNG)

Para que el servidor DHCP funcione correctamente, es necesario configurar las interfaces de red en las que estará activo. Esto se realiza editando el archivo /etc/default/isc-dhcp-server, donde añadimos la interfaz enp0s3 a interfacesv4.

![Servidor Debian](./imagenes-sri/interfaces.default-debian.png)

Configuraciones adicionales para cumplir los requisitos:

Para entrar a la ocnfiguracion de los requisitos entraremos en /etc/dhcp/dhcpd.conf 
y según entramos nos encontramos con este fichero 
![Servidor Debian](./imagenes-sri/1-dhcp.conf.png)

A continuación, editamos el archivo principal de configuración del servidor DHCP, que se encuentra en /etc/dhcp/dhcpd.conf. Realizamos las siguientes configuraciones específicas:

- Asignamos direcciones IP en el rango 10.0.200.3 - 10.0.200.100, ya que las direcciones 10.0.200.1 y 10.0.200.2 están reservadas para el router y el servidor, respectivamente.
- Establecemos la máscara de subred en 255.255.255.0.
- La puerta de enlace predeterminada será 10.0.200.1.
- El DNS utilizado será el del instituto.
- El sufijo DNS será sri200.local.
- El tiempo de alquiler será 15 días por defecto, con un máximo de 30 días y un mínimo de 1 semana.

![Servidor Debian](./imagenes-sri/2-dhcp.conf.png)

- Reservamos la dirección 10.0.200.60 para el cliente Linux
  
![Servidor Debian](./imagenes-sri/3.dhcp.conf.png)

- Una vez configurado todo, reiniciamos el servicio para aplicar los cambios.
  
![Servidor Debian](./imagenes-sri/reiniciar_debian.png)

- Finalmente, comprobamos que el servicio DHCP está activo y en ejecución correcta.
  
![Servidor Debian](./imagenes-sri/restart-status-debian.png)


## Ejercicio 2

**Windows:**

Configuramos la máquina cliente con Windows para que obtenga su dirección IP a través del servidor DHCP.

![Máquina windows](./imagenes-sri/ip-cliente-windows-dhcp.png)

Ejecutamos el comando ipconfig /all para obtener la configuración completa de la red y verificar que el cliente haya obtenido la dirección IP correctamente.

![Máquina windows](./imagenes-sri/ip-cliente-windows.png)

Hacemos un ping a la máquina Linux para comprobar la conectividad entre las máquinas en la red interna. Además, hacemos un ping a Google para confirmar la conexión a Internet.

![Máquina windows](./imagenes-sri/ping-windows.png)

**Linux**

Al igual que en Windows, configuramos el cliente Linux para que obtenga su dirección IP de forma dinámica a través del servidor DHCP. Esto se realiza editando el fichero /etc/network/interfaces.

![Máquina Linux](./imagenes-sri/ip-cliente-linux.png)

Utilizamos el comando ip a para revisar la configuración de red, y cat /etc/resolv.conf para comprobar los DNS asignados.

![Máquina Linux](./imagenes-sri/ip-a-cliente-debian.png)

Hacemos un ping a la máquina Windows para comprobar que ambas máquinas se pueden comunicar. Luego, verificamos la conexión a Internet haciendo un ping a Google.

![Máquina Linux](./imagenes-sri/ping-linux.png)

## Ejercicio 3

Utilizamos el comando journalctl -f -u isc-dhcp-server para observar los mensajes de log del servidor DHCP en tiempo real.

![Máquina Debian](./imagenes-sri/journalctl_debian.PNG)

- DHCPDISCOVER:

Es el mensaje que envía un cliente cuando está buscando un servidor DHCP en la red para solicitar una dirección IP.

- DHCPOFFER:

 El servidor DHCP responde a la solicitud DHCPDISCOVER con este mensaje, ofreciendo una dirección IP al cliente.

- DHCPREQUEST:

Una vez el cliente recibe una oferta de dirección IP, envía este mensaje para solicitar formalmente la dirección IP ofrecida.

- DHCPACK:

El servidor DHCP confirma la asignación de la dirección IP al cliente con este mensaje, indicando que ya puede ser utilizada.
