# Tarea: DNS - Docker Compose
## 1. Creamos el fichero docker-compose.yml para levantar el servidor DNS
```console
services:
  asir_dns:
    container_name: asir_dns
    image: internetsystemsconsortium/bind9:9.16
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
## 3. Crear archivos named.conf y named.conf.options
### named.conf
```console
include "/etc/bind/named.conf.options";
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
