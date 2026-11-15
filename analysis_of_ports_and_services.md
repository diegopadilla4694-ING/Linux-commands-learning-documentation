# Busqueda de servicios y Puertos activos y filtrado en Linux CLI.

Documentación para explicar la práctica sobre la enumeración de servicios de red que se esten ejecutando dentro del sistema Linux (`LISTEN`) y el uso de tuberías (*pipelines*) con `netstat`, `grep` y `wc` para filtrar salidas en sistemas Linux.

---

## Objetivo de la Práctica

Identificar cuántos y cuáles servicios o Protocolos de red que se están escuchando conexiones entrantes en todas las interfaces de red (`0.0.0.0`), descartando conexiones locales (`localhost` / `127.0.0.1`) y ruido innecesario en la terminal.

---

## Comandos Ejecutados durante la practica. 

### 1. Enumeración de puertos y filtrado de servicios en escucha.

```bash
netstat -ltun | grep "0.0.0.0" | grep -v "127*" | grep "LISTEN"
```

#### Salida obtenida:
```text
tcp        0      0 0.0.0.0:445             0.0.0.0:*               LISTEN
tcp        0      0 0.0.0.0:993             0.0.0.0:*               LISTEN
tcp        0      0 0.0.0.0:995             0.0.0.0:*               LISTEN
tcp        0      0 0.0.0.0:139             0.0.0.0:*               LISTEN
tcp        0      0 0.0.0.0:110             0.0.0.0:*               LISTEN
tcp        0      0 0.0.0.0:143             0.0.0.0:*               LISTEN
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN
```

### 2. Conteo total de servicios expuestos

```bash
netstat -ltun | grep "0.0.0.0" | grep -v "127*" | grep "LISTEN" | wc -l
```

#### Salida obtenida:
```text
7
```

---

## Desglose Técnico del Pipeline

| Segmento del Comando | Función |
| :--- | :--- |
| **`netstat -ltun`** | Lista los sockets activos:<br>• `-l`: Solo puertos en estado de escucha (*listening*).<br>• `-t`: Protocolo TCP.<br>• `-u`: Protocolo UDP.<br>• `-n`: Muestra direcciones IP y puertos numéricos (sin resolver nombres DNS). |
| **`grep "0.0.0.0"`** | Filtra únicamente los servicios vinculados a todas las interfaces de red disponibles en el host. |
| **`grep -v "127*"`** | Invierte la coincidencia (`-v`) para excluir las direcciones de *loopback* local hacia mi propio host. |
| **`grep "LISTEN"`** | Asegura que el estado del socket sea explícitamente `LISTEN`. |
| **`wc -l`** | Cuenta el número total de líneas resultantes (**7 servicios activos**). |

---

##  Servicios Identificados en el Host

Despues de un largo rato de busqueda y logica en la terminal, se identificaron los siguientes servicios expuestos en la máquina objetivo HTB:

* **Puerto `22` (SSH):** Acceso remoto seguro por consola.
* **Puerto `110` (POP3) & `995` (POP3S):** Protocolos de recepción de correo electrónico (plano y cifrado).
* **Puerto `139` & `445` (SMB / NetBIOS):** Compartición de archivos e impresoras en red.
* **Puerto `143` (IMAP) & `993` (IMAPS):** Gestión de correo electrónico en servidor (plano y cifrado).

---

## Nota Técnica: Comodines vs. Expresiones Regulares (`*` vs `.*`)

Durante la práctica de HTB se uso el caracter `*` para poder filtrar y encontrar la informacion que yo necesitaba, un comportamiento clave al usar `grep -v "127*"`:
* En **Expresiones Regulares (Regex)**, el asterisco `*` actúa como un **cuantificador** que repite el carácter inmediato anterior cero o más veces (en este caso, busca `12` seguido de cero o más `7`).
* Para representar *"cualquier secuencia de caracteres"* (como en los comodines de Bash), la sintaxis correcta en Regex es **`127.*`** o **`127\.0\.0\.1`** si se busca la dirección exacta.

---

*Práctica realizada como parte de mi formación en Administración de Sistemas Linux y Ciberseguridad (Hack The Box Academy).*