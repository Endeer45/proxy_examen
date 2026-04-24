# Guía de Infraestructura Web Redundante

Este proyecto implementa una arquitectura web de alta disponibilidad utilizando Docker y Nginx como proxy inverso.

## Características Principales

- **Acceso Único:** Todo el tráfico entra por el puerto `80`.
- **Alta Disponibilidad:** Dos servidores backend independientes (`web1` y `web2`).
- **Balanceo de Carga:** Nginx distribuye las peticiones entre los backends.
- **Seguridad y Aislamiento:** Los servidores backend están en una red interna privada, inaccesibles desde el exterior.
- **Contenido Centralizado:** Los archivos de la web se sirven desde un volumen común en `./html`.

## Justificación de la Imagen Elegida

Se ha seleccionado la imagen **`nginx:alpine`** por los siguientes motivos:
1. **Eficiencia y Tamaño:** Alpine Linux es una distribución extremadamente ligera (aprox. 5MB), lo que reduce el tiempo de descarga y el consumo de recursos.
2. **Seguridad:** Al tener una superficie de ataque mínima (menos binarios y herramientas instaladas), es intrínsecamente más segura para entornos de producción.
3. **Versatilidad:** Incluye todo lo necesario para funcionar como servidor web y proxy inverso sin necesidad de configuraciones complejas adicionales.

## Arquitectura de Red

La infraestructura utiliza un modelo de doble red para garantizar la seguridad:

- **`red_externa` (Frontend):** 
    - Es el punto de entrada. 
    - Solo el contenedor `proxy_nginx` tiene acceso a esta red.
    - Permite que los usuarios finales accedan al servicio por el puerto 80.
- **`red_interna` (Backend):** 
    - Está marcada como `internal: true`.
    - No tiene salida a internet ni es accesible desde el host directamente.
    - Conecta el `proxy_nginx` con `web1` y `web2`.
    - Garantiza que los servidores de contenido estén protegidos contra ataques directos desde el exterior.

## Estructura de Archivos

```text
.
├── docker-compose.yml   # Orquestación de contenedores y redes
├── nginx/
│   └── nginx.conf       # Configuración del balanceador de carga
├── html/                # Contenido de la web (Volumen compartido)
│   ├── index.html       # Página principal
│   ├── css/             # Estilos
│   ├── images/          # Imágenes
│   └── media/           # Multimedia
├── docs/
│   └── capturas/        # Capturas de pantalla del funcionamiento
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

## Demostración de Funcionamiento

A continuación se muestra una captura del sistema en funcionamiento, donde se aprecia la interfaz de alta disponibilidad desplegada:

![Funcionamiento del Sistema](docs/capturas/funcionamiento.png)

## Configuración Técnica del Balanceo

Se utiliza el módulo `upstream` de Nginx en `nginx.conf`:
```nginx
upstream backend_servers {
    server web1:80;
    server web2:80;
}
```
Esto permite que Nginx distribuya las peticiones de forma equitativa entre los dos nodos de backend.

