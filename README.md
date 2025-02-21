# Traefik Reverse Proxy Boilerplate

Este proyecto contiene la configuración necesaria para levantar un *reverse proxy* Traefik tanto en modo **desarrollo** como **producción**.

## Estructura
- **docker-compose.dev.yml**: Arranca Traefik en modo local (sin SSL).
- **docker-compose.prod.yml**: Arranca Traefik en modo producción (con SSL real).
- **traefik.dev.yml** / **traefik.prod.yml**: Configuración **estática** (definición de entrypoints, providers, etc.).
- **dynamic_conf.dev.yml** / **dynamic_conf.prod.yml**: Configuración **dinámica** (redirecciones, certificados, etc.).
- **acme.json**: Archivo donde Traefik almacena certificados de Let's Encrypt (opcional).
- **README.md**: Esta documentación.

## Uso en Desarrollo
1. `docker-compose up -d`
2. Accede a [http://localhost:80](http://localhost:80) para tus servicios.
3. Visita el [Dashboard de Traefik](http://localhost:8080) para ver contenedores detectados.

## Uso en Producción
1. Sitúa tus certificados reales en el servidor en la carpeta `/etc/nginx/ssl` (o ajusta la ruta).
2. Edita `dynamic_conf.prod.yml` para que `certFile` y `keyFile` coincidan con tus nombres de archivo (p.ej. `server.crt`, `server.key`, etc.).
3. `docker-compose -f docker-compose.prod.yml up -d`
4. Accede por HTTPS a tu dominio. Traefik redirigirá automáticamente HTTP → HTTPS.

## Añadir Servicios a la Red
En cada `docker-compose.yml` de tus aplicaciones:
```yaml
services:
  mi-frontend:
    image: ...
    networks:
      - proxy_net
    labels:
      - traefik.enable=true
      - traefik.http.routers.mi-frontend.rule=PathPrefix(`/`)
      - traefik.http.routers.mi-frontend.entrypoints=https
      - traefik.http.services.mi-frontend.loadbalancer.server.port=80

networks:
  proxy_net:
    external: true
