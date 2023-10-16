# Tarea: DNS - Docker Compose
## 1. Creamos el fichero docker-compose.yml para levantar el servidor DNS
```console
services:
  asir_dns:
    container_name: asir_dns
    image: ubuntu/bind9:latest
    ports:
      - 53:53
    networks:
      dns_subnet:
        ipv4_address: 172.28.5.1
    volumes:
      - ./conf:/etc/bind
      - ./zonas:/var/lib/bind
networks:
  dns_subnet:
    external: true
```
## 2. Creación de la red
```console
docker network create \
 --driver=bridge \
 --subnet=172.28.0.0/16 \
 --ip-range=172.28.5.0/24 \
 --gateway=172.28.5.254 \
 dns_subnet
```
## 3. Crear archivos named.conf, named.conf.options y named.conf.local
### named.conf
```console
include "/etc/bind/named.conf.options";
include "/etc/bind/named.conf.local";
```
### named.conf.options
```console
options {
	directory "/var/cache/bind";

	forwarders {
	 	8.8.8.8;
		1.1.1.1;
	 };
	 forward only;

	listen-on { any; };
	listen-on-v6 { any; };

	allow-query {
		any;
	};
};
```
### named.conf.local
```console
zone "asircastelao.int" {
	type master;
	file "/var/lib/bind/db.asircastelao.int";
	allow-query {
		any;
		};
	};
```
## 4. Crear en la carpeta "zonas" la base de datos del DNS
Archivo "db.asircastelao.int"
```console
$TTL 38400	; 10 hours 40 minutes
@		IN SOA	ns.asircastelao.int. some.email.address. (
				10000002   ; serial
				10800      ; refresh (3 hours)
				3600       ; retry (1 hour)
				604800     ; expire (1 week)
				38400      ; minimum (10 hours 40 minutes)
				)
@		IN NS	ns.asircastelao.int.
ns		IN A		172.28.5.1
test	IN A		172.28.5.4
web1    IN A        172.28.5.6
web2	IN A		172.28.5.7
intra	IN A		172.28.5.8
alias	IN CNAME	test
texto	IN TXT		mensaje
```
## 5. Pruebas
Accedemos a una shell en el interior del contenedor:
```console
$ docker exect -it nombre_del_contenedor bash
$ apt update
$ apt install -y dnsutils
```
Instalamos el paquete de utilidades que contiene a la herramienta "dig":
```console
$ apt update
$ apt install -y dnsutils
```
Una vez instalado, ejecutamos "dig" apuntando a "web1" utilizando como servidor DNS la dirección IP del propio contenedor:
```console
dig @172.28.5.1 web1.asircastelao.int
```
Debemos obtener una salida en la cual se nos indique que dicha dirección redirige a 172.28.5.6