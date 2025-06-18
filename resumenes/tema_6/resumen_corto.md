# Resumen Rápido de Docker

## Conceptos Clave

- **Docker**: Plataforma de virtualización ligera que usa contenedores.
- **Componentes principales**:
  - Docker Daemon (`dockerd`): Gestiona contenedores e imágenes.
  - API: Interfaz para interactuar con el servidor.
  - CLI: Interfaz de comandos para el usuario.
- **Arquitectura**: Cliente-servidor (pueden estar en el mismo sistema o en nodos diferentes).

## Funcionamiento

- **Namespaces**: Aislan recursos del contenedor:
  - PID, Network, IPC, Mount, UTS.
- **cgroups**: Limitan y monitorean recursos (CPU, memoria, I/O).
- **Union File Systems**: Capas de imágenes (inmutables) + capa escribible por contenedor.

## Definiciones

- **Imagen**: Molde de solo lectura para crear contenedores.
- **Contenedor**: Instancia en ejecución de una imagen.
- **Registry**: Almacén de imágenes (ej. Docker Hub).
- **Dockerfile**: Script para construir imágenes.

## Almacenamiento Persistente

- **Volumes**: Almacenamiento gestionado por Docker (`/var/lib/docker/volumes`).
- **Bind Mounts**: Directorios del host montados en el contenedor.

## Redes

- **Redes personalizadas**: Comunicación entre contenedores.
- **Publicación de puertos**: Hace servicios accesibles desde fuera del host.
- **DNS**: Usa los mismos servidores que el host (configurable).

## Comandos Básicos

- `docker pull IMAGEN`: Descarga una imagen.
- `docker run IMAGEN`: Ejecuta un contenedor.
- `docker run -d -p PUERTO_HOST:PUERTO_CONTENEDOR --name NOMBRE IMAGEN`: Ejecuta en segundo plano con nombre y puerto.
- `docker build -t NOMBRE:TAG .`: Construye imagen desde Dockerfile.
- `docker push USUARIO/REPOSITORIO:TAG`: Sube imagen a Docker Hub.
- `docker ps`: Lista contenedores en ejecución.
- `docker exec -it CONTENEDOR COMANDO`: Ejecuta comando dentro del contenedor.
- `docker commit CONTENEDOR REPOSITORIO:TAG`: Crea imagen desde un contenedor.

## Ejemplo de Namespaces

- Proceso en contenedor: PID 1 (dentro) vs. PID único (host).
- `lsns` muestra namespaces aislados para cada contenedor.
