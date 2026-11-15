# 🌐 Extracción y Filtrado de Rutas en HTML con Linux CLI

Documentación práctica sobre cómo realizar *web scraping* básico y enumeración de rutas directamente desde la terminal de Linux encadenando utilidades nativas (`curl`, `tr`, `grep`, `sort` y `wc`).

---

## 🎯 Objetivo de la Práctica

Descargar el código fuente HTML de un sitio web objetivo (`https://www.inlanefreight.com`), limpiar el ruido de las etiquetas HTML y extraer un listado único y contado de todas las URLs y rutas internas disponibles.

---

## 💻 Pipeline Ejecutado

```bash
curl -s https://www.inlanefreight.com | tr "\"'" "\n" | grep -E "^(https://www\.inlanefreight\.com/|/)" | sort -u | wc -l
```

*(Nota: Si se desea visualizar el listado limpio de rutas en lugar de solo el conteo total, basta con omitir `| wc -l` al final).*

---

## 🧠 Desglose Paso a Paso del Pipeline

| Comando / Segmento | Función en la Tubería |
| :--- | :--- |
| **`curl -s https://www.inlanefreight.com`** | Descarga el código fuente HTML completo de la página objetivo.<br>• `-s` (*silent*): Oculta la barra de progreso y estadísticas de descarga para no ensuciar la salida estándar (`stdout`). |
| **`tr "\"'" "\n"`** | Sanitiza el HTML reemplazando todas las comillas dobles (`"`) y comillas simples (`'`) por saltos de línea (`\n`). Esto provoca que cada atributo `href` o `src` quede aislado en su propia línea independiente. |
| **`grep -E "^(https://www\.inlanefreight\.com/\|/)"`** | Filtra únicamente las líneas relevantes usando expresiones regulares extendidas (`-E`):<br>• `^`: Exige que la coincidencia esté al inicio exacto de la línea.<br>• `https://www\.inlanefreight\.com/\|/`: Captura tanto enlaces absolutos del dominio objetivo como rutas relativas que empiezan con `/`. |
| **`sort -u`** | Ordena alfabéticamente las rutas extraídas y elimina cualquier entrada duplicada (`-u` / *unique*), descartando enlaces repetidos en menús de navegación o pies de página. |
| **`wc -l`** | Cuenta el número total de líneas únicas resultantes para obtener el total de rutas descubiertas. |

---

## ⚡ Alternativa Quirúrgica con `grep -oE`

Otra forma eficiente de extraer fragmentos específicos sin necesidad de fragmentar previamente todo el documento con `tr` es utilizando la bandera **`-o`** (*only-matching*):

```bash
curl -s https://www.inlanefreight.com | grep -oE '(https://www\.inlanefreight\.com/[^"'\'' ]*|href="/[^"]*")' | sort -u
```

* **`-o` (*Only-matching*):** Recorta y devuelve **únicamente el fragmento exacto** que coincide con la expresión regular, descartando todo el código HTML circundante en esa misma línea.

---

## 💡 Conceptos Clave Aprendidos

1. **Sanitización de texto (`tr`):** Convertir delimitadores (como comillas) en saltos de línea facilita enormemente el procesamiento posterior con herramientas orientadas a líneas como `grep` y `sort`.
2. **Anclaje de patrones (`^`):** Evita falsos positivos al asegurar que la URL o ruta relativa comience exactamente al principio de la línea limpia.
3. **Deduplicación (`sort -u`):** Paso indispensable al analizar código web real, ya que los elementos de navegación suelen repetirse múltiples veces en un mismo documento HTML.

---

*Práctica realizada como parte de mi formación en Administración de Sistemas Linux y Ciberseguridad (Hack The Box Academy).*