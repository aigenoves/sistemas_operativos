# 📌 Resumen Rápido: El Kernel de Linux y su Compilación

## 🧠 Fundamentos del Kernel

- Parte central del SO que administra recursos y proporciona servicios.
- Kernel Linux: software libre, escrito en C (con algo de Assembler y Rust).
- Tipo: **monolítico**, pero modular (con soporte de módulos).

### 🔧 Funciones Principales

- Gestión de memoria.
- Gestión de CPU y procesos.
- Comunicación, concurrencia, E/S.
- Acceso seguro al hardware vía *System Calls*.

### 🧩 Modos de Ejecución

- **Modo Kernel**: acceso completo al hardware.
- **Modo Usuario**: acceso restringido.
- Cambio de modo: solo mediante interrupciones (TRAPs).

## 🏗️ Arquitecturas de Kernel

- **Monolítico**: todo en un solo bloque (Linux, Unix).
- **Microkernel**: funciones mínimas en modo kernel (Minix, QNX).
- **Híbrido**: mezcla entre ambos (Windows, MacOS).
- **ExoKernel**: acceso directo al hardware, mínima abstracción.

## 🧮 Capacidad de CPU

- Fórmula: `capacity(cpu) = work_per_hz(cpu) * max_freq(cpu)`
- Considerada por el *scheduler* para sistemas heterogéneos.

---

## 🔧 Compilación del Kernel: Pasos

### 1️⃣ Obtener el código fuente

- Desde [kernel.org](https://www.kernel.org)
- Verificar integridad con firma GPG.

### 2️⃣ Preparar árbol de archivos

- Se puede usar `/usr/src` o `$HOME`.
- Enlace simbólico a `linux`.

### 3️⃣ Configurar el kernel

- Archivo `.config`: define qué se compila.
- Herramientas: `make menuconfig` (recomendada), `xconfig`, `config`.

#### 📦 Módulos del Kernel

- Fragmentos de código cargables dinámicamente.
- Se ubican en `/lib/modules/version`.
- Comando útil: `lsmod`.

**Built-in vs Módulo**

- `Built-in`: más memoria, arranque lento, acceso inmediato.
- `Módulo`: menor memoria, carga bajo demanda, más flexible.

### 4️⃣ (Opcional) Aplicar parches

- Usar `patch -p1` con `--dry-run` para probar antes de aplicar.

### 5️⃣ Compilar e instalar

- Requiere: `GCC`, `as`, `ld`, `make`.
- Comandos:
  - `make`: compila el kernel.
  - `make modules`: compila módulos.
  - `sudo make install`: instala el kernel.
  - `sudo make modules_install`: instala módulos.

### 6️⃣ Crear `initramfs`

- Sistema de archivos temporal usado al inicio.
- Comando: `mkinitramfs -o /boot/initrd.img-VERSION VERSION`.

### 7️⃣ Configurar GRUB

- Comando: `update-grub2`.

### 8️⃣ Reiniciar y probar

---

✅ ¡Listo para compilar y entender el kernel de Linux!
