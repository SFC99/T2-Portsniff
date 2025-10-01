# 📟 Proyecto: Escáner híbrido de puertos y sniffing en C++ 📟

---

## 🧠 1. Descripción general

Herramienta desarrollada en **C++17** para **Linux**, cuyo objetivo es realizar un **escaneo real de puertos TCP y UDP** sobre un host objetivo y capturar la primera trama de respuesta mediante técnicas de **sniffing**.  

El programa genera un **informe JSON** con la información del servicio detectado, el estado del puerto y los primeros bytes de la cabecera **IP/TCP o IP/UDP**.

El desarrollo sigue un enfoque **modular, concurrente y con captura en tiempo real**, utilizando **libpcap** y **sockets no bloqueantes** para optimizar el rendimiento y la precisión del análisis.

---

## 👥 2. Integrantes del equipo

| 👨‍💻 **Integrante** | 🧩 **Módulo asignado** | 📝 **Descripción** |
|----------------------|------------------------|--------------------|
| **Juan Angel Rodríguez Bulnes** | 🔎 *Escaneo* | Implementación del escaneo **TCP/UDP** con **sockets no bloqueantes**, gestión de **timeouts** y clasificación de estados (*abierto*, *cerrado*, *filtrado*). |
| **Sofía Martínez Cisneros** | 📡 *Sniffer* | Captura de paquetes con **libpcap**, configuración de filtros **BPF dinámicos** por IP y puerto, y extracción de **bytes de cabecera**. |
| **Angel David Morales Palomo** | 🧾 *JSONGen* | Generación manual del archivo **JSON** usando **streams de C++**, garantizando compatibilidad, portabilidad y control total sobre el formato. |

---

## 💻 3. Requisitos del sistema

- 🧱 **Sistema operativo:** Lubuntu (o cualquier distribución basada en Ubuntu/Debian)  
- 🧠 **Compilador:** `g++` compatible con **C++17 o superior**  
- 📦 **Dependencias:**  
  - `libpcap` → Captura de paquetes en red  
  - `pthread` → Manejo de hilos para concurrencia  

---

✨ *Este proyecto fue diseñado para integrar técnicas de redes, concurrencia y análisis de tráfico en un entorno práctico y eficiente.*  

---

# ⚙️ Instalación de dependencias

Ejecuta los siguientes comandos en un entorno **Linux** para instalar las herramientas necesarias:

```bash
sudo apt update
sudo apt install g++ libpcap-dev make
```

---

## 🏗️ 4. Compilación y ejecución

El proyecto utiliza un **Makefile** para una compilación reproducible.

### 🔧 Compilación

```bash
make
```

Esto genera el ejecutable:

```
portsniff
```

### ▶️ Ejecución

```bash
./portsniff
```

El programa solicitará los siguientes datos:

```
IP objetivo: 192.168.1.100
Puerto inicial: 20
Puerto final: 1024
Timeout (ms): 500
Archivo JSON de salida: resultado.json
```

---

## 🧩 5. Enfoque técnico

### 🔍 Escaneo de puertos

#### **TCP**
- Uso de `connect()` no bloqueante y `select()` para detección de *timeouts*.
- Clasificación: **Abierto**, **Cerrado** o **Filtrado**.

#### **UDP**
- Envío de datagramas con `sendto()`.
- Detección de puertos cerrados mediante ICMP “port unreachable”.

---

### 🧪 Sniffing
- Uso de **libpcap** en modo promiscuo.
- Filtros **BPF dinámicos** según IP y puertos escaneados.
- Captura en tiempo real y extracción de los primeros **16 bytes** de cabecera IP/TCP o IP/UDP.

---

### ⚙️ Concurrencia
- Ejecución simultánea de **escaneo y captura** mediante `std::thread`.
- Sincronización con **mutex** y **colas seguras**.

---

### 🧾 Generación del JSON
- Sin bibliotecas externas (no se usa `nlohmann/json`).
- Creación manual con **streams de C++ estándar**.
- Control total del formato y estructura final.

---

## 📄 6. Formato y estructura del JSON generado

El archivo de salida (por ejemplo, `resultado.json`) conserva la estructura base del enunciado, agregando el campo `"state"` para mayor detalle.

### Ejemplo:

```json
[
  {
    "ip": "192.168.1.100",
    "port": 22,
    "protocol": "TCP",
    "service": "ssh",
    "state": "abierto",
    "header_bytes": "45 00 00 34 12 34 40 00"
  },
  {
    "ip": "192.168.1.100",
    "port": 161,
    "protocol": "UDP",
    "service": "snmp",
    "state": "abierto",
    "header_bytes": "45 00 00 2c 56 78 00 00"
  }
]
```

---

### Campos del JSON:

| **Campo** | **Descripción** |
|------------|-----------------|
| `ip` | Dirección IP del host objetivo |
| `port` | Número de puerto |
| `protocol` | Protocolo usado (TCP o UDP) |
| `service` | Servicio detectado (si aplica) |
| `state` | Estado del puerto (abierto, cerrado, filtrado) |
| `header_bytes` | Primeros bytes capturados de la cabecera IP/TCP o IP/UDP |

---

## 📁 7. Estructura del proyecto

```
src/
 ├── main.cpp
 ├── Escaneo.cpp
 ├── Sniffer.cpp
 ├── JSONGen.cpp
 ├── Escaneo.h
 ├── Sniffer.h
 └── JSONGen.h
Makefile
README.md
```

---

## 💻 8. Ejemplo de ejecución real

```bash
sudo ./portsniff
```

### Salida esperada:

```
Escaneando host 192.168.1.100...
Puerto 22 (TCP): Abierto - Servicio: ssh
Puerto 53 (UDP): Abierto - Servicio: dns
Puerto 80 (TCP): Abierto - Servicio: http
Puerto 161 (UDP): Cerrado
Archivo resultado.json generado correctamente.
```
