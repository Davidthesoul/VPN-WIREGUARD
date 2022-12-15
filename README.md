# VPN-WIREGUARD
Descripcion de Creación server VPN en ubuntu server con wireguard

#Actualización de repositorios
apt update && apt upgrade -y

#Instalación de Wireguard
apt install wireguard

#Acceso a la carpeta de instalación
cd /etc/wireguard

#Seteado de permisos solo para usuario root
umask 077

#Creación de pares de claves pública y privada
wg genkey |tee 00_server_clave_privada | wg pubkey > 00_server_clave_publica
wg genkey |tee 01_cliente_clave_privada | wg pubkey > 01_ciente_clave_publica


####### creacion de cliente vpn  SEGUIR CORRELATIVO ########## 
####PASO 1: Creación de las claves publica y privada del cliente ####
wg genkey |tee 0X_cliente_clave_privada | wg pubkey > 0X_ciente_clave_publica

####PASO 2: verificación de que se han creado correctamente las claves ######
ls -lh
####PASO 3: ver las claves publicas y privada, por defecto las 2 ultimas y copiarlas  ######
cat 0*
####PASO 4: Copiar ambas claves por defecto la penultima es publica y la ultima la privada #####





#Listar interfaces de red
ip addr

#Creación de archivo de texto con configuración del servidor
nano wg0.conf

#CONFIGURACIÓN DEL SERVER
###################################################

[Interface]
PrivateKey = <Clave privada del server>
Address = 10.0.0.1/32
PostUp = iptables -A FORWARD -i %i -j ACCEPT; iptables -A FORWARD -o %i -j ACCEPT; iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
PostDown = iptables -D FORWARD -i %i -j ACCEPT; iptables -D FORWARD -o %i -j ACCEPT; iptables -t nat -D POSTROUTING -o eth0 -j MASQUERADE
ListenPort = 51820

[Peer]
PublicKey = <Clave pública del cliente>
AllowedIPs = 10.0.0.2/32

####################################################

#Levantado de la conexión VPN en el servidor
wg-quick up wg0

#Persistencia del interfaz de conexión
systemctl enable wg-quick@wg0

#Activación del reenvío de paquetes
sysctl -w net.ipv4.ip_forward=1
nano /etc/sysctl.conf


#CONFIGURACIÓN DEL CLIENTE 
#####################################################

[Interface]
PrivateKey = <Clave privada cliente>
Address = 10.0.0.2/32
DNS = 8.8.8.8

[Peer]
PublicKey = <Clave pública server>
AllowedIPs = 0.0.0.0/0
Endpoint = IP_Server:51820

######################################################

Footer
© 2022 GitHub, Inc.
Footer navigation
Terms
Privacy
Security
