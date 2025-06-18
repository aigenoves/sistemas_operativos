
# 📌 Resumen Rápido: System Calls, Módulos y Drivers en GNU/Linux

---

## 🔗 System Calls (Llamadas al Sistema)

- Mecanismo que permite a procesos de **usuario acceder a servicios del kernel**.
- Usadas para: **gestión de procesos, memoria, archivos, dispositivos, IPC**.
- Solo forma de pasar de **modo usuario (Ring 3)** a **modo kernel (Ring 0)**.

### 🔄 Funcionamiento General

1. Proceso coloca **parámetros** en la pila.
2. Llama a una función de **`libc` (wrapper)**.
3. Se ejecuta una **interrupción por software** (`int 0x80`, `syscall`, `svc`) para pasar a modo kernel.
4. El kernel busca el **handler** correspondiente en la **tabla de syscalls** y lo ejecuta.
5. Devuelve el control al proceso de usuario.

### 🧱 Interfaces y Seguridad

- Interfaz principal: **`libc` + estándar POSIX**.
- El kernel valida punteros con APIs seguras como:
  - `copy_from_user()`
  - `copy_to_user()`

### 🔧 Desarrollo de Syscalls

- Requiere:
  - **Número único**.
  - Entrada en tabla de syscalls (ej: `syscall_64.tbl`).
  - Función con prefijo `sys_` y macro `asmlinkage`.
  - Recompilación del kernel.

### 🧪 Herramientas y Ejemplos

- **`strace`**: Monitorea syscalls usadas por procesos.
- Ejemplos: `read()`, `chmod()`, `fork()`, `clone()`, `unshare()`, `setns()`, `getpid()`.

---

## 🧩 Módulos del Kernel

- Pedazos de código que se **cargan y descargan dinámicamente** en el kernel.
- Permiten extender la funcionalidad del sistema sin reiniciarlo (modo **hotplug**).
- Ejecutan en **modo kernel**, pueden causar **Kernel Panic** si fallan.

### ⚖️ Built-in vs Módulo

- **Built-in**:
  - Mayor uso de memoria.
  - Acceso más eficiente.
- **Módulo**:
  - Menor consumo hasta ser cargado.
  - Modificable sin recompilar kernel.

### 🛠️ Comandos Útiles

- `lsmod`: Lista módulos cargados.
- `insmod`: Carga un módulo.
- `rmmod`: Descarga un módulo.
- `modinfo`: Muestra info del módulo.
- `depmod`: Resuelve dependencias.
- `modprobe`: Carga con dependencias.

### 🧪 Implementación

- Requiere funciones:
  - `init_module` (al cargar).
  - `cleanup_module` (al descargar).
- Usar `printk()` para logs (no `printf`).
- Compilar con `make` y `Makefile` específico.
- Se instalan en `/lib/modules/<versión>`.

---

## 💾 Drivers de Dispositivos

- Código que permite **controlar hardware específico** desde el SO.
- En UNIX/Linux, los dispositivos se representan como **archivos en `/dev`**.

### 📚 Tipos de Dispositivos

- **Caracter**: Acceso secuencial (ej. mouse).
- **Bloque**: Acceso aleatorio (ej. discos).

### 🔧 Acceso a Dispositivos

- Operaciones: `read()`, `write()`, `ioctl()`.
- Se identifican por **Major** y **Minor number**.
- Crear con `mknod` (`b` o `c` según tipo).

### 🔌 Implementación en el Kernel

- Basado en `struct file_operations` con punteros a funciones:
  - `read`, `write`, `open`, `release`, etc.
- Manejo seguro de punteros con:
  - `copy_from_user()`
  - `copy_to_user()`

---

✅ Este resumen te permite repasar rápido los conceptos clave sobre **interacción entre usuario y kernel**, **extensibilidad dinámica** mediante módulos y **gestión de hardware** mediante drivers.
