# 🧵 Resumen Rápido de Hilos (Threads)

## 🔄 Procesos vs. Hilos

### 🧱 Procesos

- Tienen su propio espacio de direcciones y un único flujo de control.
- No comparten memoria (salvo mediante Copy-On-Write con `fork()`).
- Cambio de contexto costoso (requiere intervención del SO).
- Creación/destrucción más lenta.
- Pueden generar procesos "zombie" si no se hace `wait()`.

### 🧵 Hilos

- Unidad de ejecución dentro de un proceso, conocidos como LWP.
- Comparten código, datos, heap; cada hilo tiene su propio stack.
- Cambio de contexto más rápido (no cambia el espacio de direcciones).
- Comunicación más fácil (memoria compartida).
- Menor protección: el programador debe cuidar la concurrencia.

## 🧩 Tipos de Hilos

### ULT (User-Level Threads)

- Gestionados por biblioteca en espacio de usuario.
- ✅ Rápidos y portables, permiten planificación personalizada.
- ❌ No aprovechan paralelismo real, se bloquea todo el proceso con una syscall bloqueante.

### KLT (Kernel-Level Threads)

- Gestionados por el Kernel.
- ✅ Soportan paralelismo real, syscall bloqueante no afecta a otros hilos.
- Ej: PThreads en Linux (NPTL).

### LWP (Lightweight Processes)

- Nivel intermedio, visibles para el Kernel.
- Permiten modelos híbridos M:N (ej: Go).

## 🔄 Comunicación y Sincronización

- 🧠 Memoria compartida: vía variables globales/punteros.
- 🔒 Mecanismos:
  - `pthread_mutex_t` – exclusión mutua.
  - `pthread_cond_t` – sincronización condicional.
  - `pthread_barrier_t` – espera de grupo.
  - `sem_init`, `sem_open` – semáforos.

## ⚙️ Funciones Comunes

### PThreads (Linux)

- `pthread_create()` – crear hilo.
- `pthread_join()` / `pthread_detach()` – esperar o liberar recursos.
- `pthread_self()` / `gettid()` – obtener identificador.

### GNU Pth (ULT)

- `pth_init()` – inicializar.
- `pth_spawn()` – crear hilo.
- `pth_join()`, `pth_self()` – esperar/obtener ID.

## 💬 Hilos en Lenguajes Interpretados

- **Python / Ruby** usan KLT con **GIL** (limitación de paralelismo real).
- Recomendados para tareas IO-bound.
- **Go** usa **Goroutines** (ULT gestionadas por su runtime).

## 🔍 Herramientas Útiles

- `htop` – monitoreo de CPU/hilos.
- `strace` – trazado de syscalls.
- `printk` – debug en Kernel (similar a `printf`).
