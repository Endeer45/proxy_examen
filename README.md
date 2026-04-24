# Guía de Infraestructura Web Redundante

Este proyecto implementa una arquitectura web de alta disponibilidad utilizando Docker y Nginx como proxy inverso.

## Características Principales

- **Acceso Único:** Todo el tráfico entra por el puerto `80`.
- **Alta Disponibilidad:** Dos servidores backend independientes (`web1` y `web2`).
- **Balanceo de Carga:** Nginx distribuye las peticiones entre los backends.
- **Seguridad y Aislamiento:** Los servidores backend están en una red interna privada, inaccesibles desde el exterior.
- **Contenido Centralizado:** Los archivos de la web se sirven desde un volumen común en `./html`.

## Estructura de Archivos

```text
.
├── docker-compose.yml   # Orquestación de contenedores y redes
├── nginx/
│   └── nginx.conf       # Configuración del balanceador de carga
├── html/                # Contenido de la web (Volumen compartido)
│   ├── index.html       # Página principal
│   ├── css/             # Estilos
│   ├── images/          # Imágenes (incluye hero.png generado)
│   └── media/           # Multimedia
└── README.md            # Documentación (este archivo)
```

## Requisitos
- Docker
- Docker Compose

## Despliegue

Para poner en marcha el entorno, ejecuta el siguiente comando en la raíz del proyecto:

```bash
docker-compose up -d
```

### Verificación
- Accede a `http://localhost`.
- Para ver el balanceo en tiempo real, puedes revisar los logs de los contenedores:
  ```bash
  docker-compose logs -f
  ```

## Configuración Técnica

### Redes
- **red_externa (Frontend):** Conecta el proxy con el exterior.
- **red_interna (Backend):** Red aislada (`internal: true`) para la comunicación entre el proxy y los servidores web.

### Balanceo
Se utiliza el módulo `upstream` de Nginx:
```nginx
upstream backend_servers {
    server web1:80;
    server web2:80;
}
```
