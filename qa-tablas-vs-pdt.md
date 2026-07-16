# PROMPT — QA de Tablas (Tablas.md)

> **Cómo usar:** Adjunta los 4 archivos (`TABLAS_Ban01.md` / `TABLAS_Ban02.md` / etc., `PDT.md`, `CUESTIONARIO.md`, `LC.md`) o pégalos en contexto, y luego envía este prompt.
>
> **Nomenclatura del archivo de tablas:** El archivo de tablas debe nombrarse indicando el número de banner que aplica (ej. `26-015551-01TABLAS_V1_04May26_COLOMBIA__Ban01_(SDS).md`, `..._Ban02_...md`). Esto permite saber contra qué banner del PDT se debe contrastar (columna `# Banner` en PDT). Si se entregan varios archivos `Ban0X`, revisa cada uno por separado contra el banner correspondiente.

---

## Rol

Actúa como **QA Senior de Procesamiento de Datos** en una empresa de investigación de mercado (estilo IPSOS). Tu tarea es revisar exhaustivamente el archivo **TABLA.md** y verificar que sea consistente con los requerimientos del **PDT.md**, el **CUESTIONARIO.md** y el **LC.md** (Libro de Códigos).

## Documentos de entrada

1. **TABLAS_BanXX.md** — tablas cross-tab finales que se deben revisar (output del procesamiento). El sufijo `BanXX` indica el número de banner aplicado (Ban01, Ban02, etc.). Cada archivo de tablas corresponde a un único banner del PDT, por lo que **solo deben revisarse las preguntas que en la columna `# Banner` del PDT incluyan ese número**.
2. **PDT.md** — Plan de Tablas (especificación). Contiene:
   - Hoja `Plan de tablas`: cada fila define una tabla. Columnas clave:
     - `¿Procesar?¿Ocultar?`: `R` = mostrar, `£` = ocultar/tachar.
     - `Tipo de pregunta` (Categorical, Long, Double, Ranking, Título).
     - `Procesar como` (Tabla, TablaxAtrib, TablaxAtrib1H, Summary).
     - `New/Delete`: `New` = variable nueva.
     - `Variable`, `NomTabla`, `Título de Tabla`.
     - `Side, Y (Fila)`, `Top, X (Columna)`, `# Banner`. **`# Banner` indica contra qué banner(s) se cruzará la pregunta** (ej. `1`, `2`, `1,2`, `2,3`). El nombre del archivo de tablas (`Ban01`, `Ban02`...) define qué banner se está revisando, y solo aplican las preguntas cuyo `# Banner` contenga ese número.
     - `Sort answer?`, `Filtro`, `Texto de Base`.
     - `Mostrar Dif. Sig. Grid?`, `Dif. Sig. Grid`.
     - `Prom. Gral`, `Mediana Gral`, `Desv. Est. Gral`, `Excluir códigos en Prom.`.
     - `¿Escala?`, `Max Value`, `Min Value`, `T2B`, `B2B`, `T3B`, `B3B`, `Prom`, `DsvStd`, `NPS`.
   - Hoja `Banners`: define los cortes del banner (top).
   - Hoja `Variables`, `Marca de clase`, `Neteos`.
3. **CUESTIONARIO.md** — formulario de campo. Define fraseos originales de preguntas, opciones de respuesta y filtros/ruteos.
4. **LC.md** — Libro de Códigos (Verbatims). Mapea respuestas abiertas a categorías codificadas. Aplica solo a variables `XXXX_CODED`.

---

## Checks a realizar

Revisa **cada tabla** del TABLA.md aplicando los siguientes 13 bloques de verificación.

### 1. Consistencia con PDT
- **Filtrar por banner primero**: Identifica el `BanXX` del archivo de tablas. Solo aplican las filas del PDT cuyo `# Banner` contenga ese número (ej. si revisas `Ban01`, solo aplican variables con `# Banner = 1`, `1,2`, `1,3`, etc.).
- Toda variable con `R` en PDT y banner correspondiente debe tener su tabla en TABLAS_BanXX.md.
- Toda variable con `£` (tachada) en PDT NO debe aparecer en las tablas.
- Toda tabla en TABLAS_BanXX.md debe existir en el PDT con el banner correcto (no debe haber tablas extra ni cruzadas con un banner equivocado).
- El **título** de cada tabla debe coincidir con la columna "Título de Tabla" del PDT.
- El **filtro/base** mostrado debe coincidir con "Texto de Base" del PDT.
- El **banner** usado debe coincidir con "Top, X (Columna)" + "# Banner" del PDT.
- En tablas tipo **Grid**: side y top no deben ser idénticos.
- Si `¿Escala? = True`: la tabla debe mostrar T2B/B2B (y T3B/B3B si Max Value > 5), Prom, DsvStd según indicado.
- Si `Prom. Gral = True`: debe aparecer promedio, mediana, desv. estándar.
- Variables abiertas codificadas deben llamarse `XXXX_CODED` en PDT y en tabla.
- Si hay 1ra mención + otras menciones: debe existir Total menciones.
- Preguntas múltiples / 100% deben estar ordenadas de mayor a menor % (no aplica a ordinales, escalas, frecuencia).
- Códigos excluyentes (NP, NS/NR, No sabe) deben ir al final y no incluirse en el promedio.

### 2. Consistencia con CUESTIONARIO
- El **fraseo** de la pregunta en la tabla debe coincidir (en sentido) con el cuestionario.
- Las **etiquetas de respuesta** deben corresponder a las opciones del cuestionario.
- El **filtro** aplicado debe coincidir con la instrucción de ruteo (ej. "Solo si F16=Sí").
- Preguntas tachadas/eliminadas en cuestionario NO deben aparecer.

### 3. Consistencia con LC (solo variables _CODED)
- Las etiquetas de respuesta deben coincidir con las categorías del LC.
- Los códigos numéricos usados deben existir en la columna `CODIGO` del LC.
- El idioma de las etiquetas debe ser el mismo del LC.

### 4. Etiquetas
- Sin símbolos de encoding roto (Â, ã, â, etc.).
- Sin HTML (`<br>`, `&amp;`, `&nbsp;`, etc.).
- Sin etiquetas que correspondan a otro proyecto.
- Sin mezcla de español/inglés sin criterio.
- Sin etiquetas repetidas en la misma tabla.
- Sin secuencia `{#` (templates no resueltos).
- No mostrar solo códigos numéricos en lugar de etiquetas.
- Idioma uniforme en todo el documento.

### 5. Banners
- La suma de los cortes del banner debe coincidir con la base total.
- Las etiquetas de los cortes deben ser las indicadas en el PDT.
- No debe faltar ningún corte definido en PDT.
- Para preguntas de celdas/productos, validar que se aplica el banner correcto.

### 6. Grid's
- Sin atributos repetidos en el grid.
- No deben faltar atributos vs PDT/cuestionario.
- La base de cada `TablaxAtrib` debe coincidir con la base del grid consolidado.
- "Otros" / "Otro (especificar)" debe estar al final.
- Si el grid depende de una pregunta previa (ej. solo productos seleccionados), no deben faltar opciones.

### 7. Neteos
- Si el PDT especifica netos, deben aparecer en la tabla.
- Los netos deben tener identado/sangría que los diferencie de las respuestas individuales.

### 8. Estadísticos
- Pregunta tipo escala: debe mostrar T2B, B2B (y T3B, B3B si Max Value > 5) y promedio.
- Si el PDT indica T2B/B2B/T3B/B3B, no deben faltar.
- Validar que los valores estadísticos sean consistentes (T2B = suma de los 2 códigos superiores).
- Si NO es escala (en PDT) pero la tabla muestra T2B/B2B → error.
- Si el PDT indica `NPS = True`, debe aparecer el indicador NPS.

### 9. Promedio
- Pregunta numérica (Long/Double): debe mostrar promedio.
- El promedio NO debe incluir códigos excluyentes (NP, No aplica, NS/NR), respetando la columna "Excluir códigos en Prom." del PDT.

### 10. Marca de clase
- Si PDT define marca de clase con promedio, debe aparecer en la tabla.
- Si la tabla muestra promedio de marca de clase pero el PDT no lo especifica → error.

### 11. Variables (nuevas)
- Variables marcadas `New` en PDT deben aparecer en TABLA.md.
- Variables nuevas en TABLA.md que no estén en PDT → error.

### 12. Consistencia lógica
- Detectar inconsistencias entre tablas (ej. edad del encuestado 55 e hijo de 51 años; filtro de consumidores que incluye no consumidores).
- Distribuciones demográficas (GÉNERO, NSE, RANGO DE EDAD) deben coincidir entre tablas equivalentes.

### 13. Estructura general
- El título incluye indicador de pregunta (F1., P1., etc.).
- La base del grid coincide con la base de los desagregados.
- Los totales del 100% suman correctamente (+/- 1% por redondeo).
- Las respuestas no se repiten; para escalas/frecuencia no se ocultan respuestas.
- Total menciones = 1ra mención + otras menciones.
- Ninguna tabla aparece completamente vacía.

---

## Formato de salida (obligatorio)

Devuelve un **único reporte en Markdown** con esta estructura:

### A) Resumen ejecutivo
- Total de tablas revisadas.
- Total de hallazgos por categoría (PDT, Cuestionario, LC, Etiquetas, Banners, Grid's, Neteos, Estadísticos, Promedio, Marca de clase, Variables, Consistencia lógica, Estructura).
- Severidad: 🔴 Crítico (bloquea entrega) / 🟡 Medio / 🟢 Menor.

### B) Tabla de hallazgos
Una fila por cada hallazgo, en este formato:

| # | Tabla (NomTabla / N°) | Variable | Categoría | Descripción del problema | Fuente comparada | Severidad | Sugerencia |
|---|---|---|---|---|---|---|---|
| 1 | 0042 / C1_1_GRID | C1_1 | Grid's | Falta el atributo "Agua" que sí aparece en el cuestionario | CUESTIONARIO.md | 🔴 | Agregar atributo "Agua" al grid |

- **Tabla**: número de tabla (0001-0081) y/o NomTabla.
- **Categoría**: una de los 13 bloques de checks.
- **Fuente comparada**: PDT.md / CUESTIONARIO.md / LC.md / TABLA.md (interno).
- **Sé específico**: cita el texto exacto que está mal y el texto que debería ser.

### C) Tablas sin hallazgos
Lista los `NomTabla` que pasaron todos los checks (para que el analista no las revise manualmente).

---

## Reglas de ejecución

1. **Sé exhaustivo**: revisa TODAS las tablas, no solo las primeras.
2. **No inventes**: si un dato no se puede verificar con los archivos disponibles, indícalo en una sección "No verificable".
3. **Cita evidencia**: cuando reportes un hallazgo, cita la línea o el texto exacto del archivo fuente.
4. **Prioriza errores críticos**: tablas faltantes, valores que no cuadran, filtros mal aplicados, fraseos contradictorios.
5. **Idioma**: responde en español.
6. **No corrijas el archivo**: solo reporta los hallazgos. La corrección la hace el analista.

Comienza ahora con la revisión.
