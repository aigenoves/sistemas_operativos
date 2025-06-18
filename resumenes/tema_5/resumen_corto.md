# Resumen: cgroups, Namespaces y Contenedores

## 📌 `chroot`

- Cambia el directorio raíz aparente de un proceso ("jail chroot").
- Aísla procesos y sus hijos, impidiendo acceso fuera del nuevo `/`.
- Comparte el mismo kernel del host.
- Precursor de contenedores, usado en Docker para el filesystem.

## 📌 Control Groups (`cgroups`)

- **Propósito**: Limitar/monitorear recursos (CPU, memoria, I/O).
- **Características**:
  - Jerarquías de procesos.
  - Subsistemas (`cpu`, `memory`, etc.).
  - Límites, priorización, contabilidad y control (congelar procesos).
- **Versiones**:
  - **v1**: Subsistemas independientes, procesos en múltiples jerarquías.
  - **v2**: Jerarquía unificada, procesos solo en "hojas".

## 📌 Namespaces

- Aíslan recursos globales (procesos ven su propia instancia).
- **Tipos**:
  - `PID` (árbol de procesos), `Network` (red), `Mount` (filesystem).
  - `User` (UIDs/GIDs), `UTS` (hostname), `IPC` (comunicación).
  - `Cgroup` (cgroups raíz), `Time` (reloj).
- **Mecanismo**:
  - `clone()`, `unshare()`, `setns()`.
  - Namespaces se eliminan al finalizar el último proceso.

## 📌 Contenedores (Docker/Podman)

- **Bases**: `namespaces` (aislamiento) + `cgroups` (recursos) + `chroot` (filesystem).
- **Docker**:
  - **Imagen**: Molde de solo lectura (capas con `Union FS`).
  - **Contenedor**: Instancia ejecutable (capa escribible efímera).
  - **Volúmenes**: Persistencia de datos (`volumes` o `bind mounts`).
- **Podman**:
  - Sin daemon, compatible con comandos Docker.
  - **Pods**: Grupos de contenedores que comparten namespaces (red, PID, IPC).
  - Contenedor `infra` mantiene namespaces del Pod.

## 🔗 Estándares

- **OCI**: Especificaciones para runtimes (`runc`) e imágenes.
- **Orquestadores**: Kubernetes, Docker Swarm (gestión de clusters).
