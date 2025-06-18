# Resumen Rápido: Docker Compose, Podman y Estandarización

## Docker Compose
- **Herramienta** para gestionar aplicaciones multi-contenedor (servicios).
- Configuración en archivo YAML (`compose.yaml` o `docker-compose.yml`).
- **Versiones**: 3.x (recomendada, ej. 3.9 desde 2020).
- **Ventajas**:
  - Simplicidad (configuración centralizada).
  - Replicabilidad y compartibilidad.
  - Integración con control de versiones.
- **Comandos clave**:
  - `docker compose up -d`: Inicia servicios en segundo plano.
  - `docker compose down`: Detiene y elimina contenedores.
  - `docker compose logs`: Muestra registros.
  - `docker compose ps`: Lista contenedores activos.
- **Funcionalidades**:
  - Políticas de reinicio.
  - Dependencias entre servicios.
  - Redes personalizadas (red `default` por defecto).

---

## Podman
- **Motor de contenedores "daemonless"** (sin demonio central).
- Compatible con imágenes OCI y Docker.
- **Características**:
  - Usa `libpod` para gestión del ciclo de vida.
  - Soporte rootless (sin privilegios root).
  - Aislamiento con cgroups.
- **Pods**:
  - Grupos de contenedores que comparten namespaces (red, IPC, etc.).
  - Contenedor `infra` mantiene namespaces activos.
  - Proceso `conmon` monitorea contenedores y comunica con el runtime OCI.
- **Ventajas de Pods**:
  - Comparten IP, almacenamiento y configuración.
  - Comunicación vía `localhost`.

---

## Estandarización
- **OCI (Open Container Initiative)**:
  - Estándares para imágenes y runtimes (ej. `runc`).
- **CRI (Container Runtime Interface)**:
  - Plugin para integrar runtimes en Kubernetes (ej. CRI-O de RedHat).
- **Evolución**:
  - Docker usó LXC → `libcontainer` → `containerd` + `runc`.
  - CoreOS lanzó `rkt` (adoptado por CNCF).

---

## Orquestadores
- **Cluster**: Grupo de nodos interconectados.
- **Herramientas**:
  - Kubernetes (el más popular).
  - Docker Swarm, RedHat Openshift, Rancher.
- **Funciones**:
  - Automatizar despliegue, escalado y gestión de contenedores.