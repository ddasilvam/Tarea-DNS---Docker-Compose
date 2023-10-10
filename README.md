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
```
