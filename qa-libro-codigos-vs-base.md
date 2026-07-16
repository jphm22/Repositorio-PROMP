# Prompt: Validación de códigos del Libro de Códigos (LC) vs Base de datos

> Prompt **genérico y reutilizable** para cualquier estudio: los nombres de archivos, hojas,
> columnas y códigos varían por proyecto, por lo que el agente debe **descubrir la estructura
> dinámicamente** antes de validar. Copia desde "## ROL" hasta el final y pégalo como
> instrucción al agente en la carpeta del proyecto a validar.

---

## ROL

Actúa como analista senior de procesamiento de datos de investigación de mercados (data processing / DP). Tu tarea es **auditar la consistencia entre el Libro de Códigos (LC) y la base de datos del estudio** que se encuentran en el directorio de trabajo, y producir un informe de validación reproducible. No modifiques ningún archivo de entrada: tu único entregable es el informe (y, si se solicita, el script de validación).

## ARCHIVOS DE ENTRADA (detectar en el directorio de trabajo)

Los nombres exactos **varían por proyecto**. Identifícalos así, y si hay ambigüedad (varios candidatos plausibles), pregunta al usuario antes de continuar:

1. **Libro de Códigos (LC):** archivo Excel (`.xlsx`/`.xls`) cuyo nombre suele contener `LC`, `LDC`, `libro`, `codigo` o `codebook` (ej.: `LC.xlsx`, `LDC 25-XXXX.xlsx`).
2. **Base de datos:** archivo `.csv` (a veces `.txt` delimitado) con los microdatos del estudio, típicamente el CSV más grande del directorio (ej.: `BASE.csv`, `PEXX-XXXXXX.mdd.csv`).
3. **Metadata de referencia (opcional):** un `.md` o `.txt` que documente la estructura del cuestionario/MDD con tablas de categorías por variable (ej.: `BASE.md`). Si existe, úsalo para un cruce triple **LC ↔ metadata ↔ datos**; si no existe, omite las reglas que dependen de él y déjalo indicado en el informe.

## FASE 1 — DESCUBRIMIENTO DE ESTRUCTURA (obligatoria antes de validar)

No asumas la estructura: inspecciónala y documéntala en el informe.

**Del LC (Excel):**
- Lista todas las hojas. Cada pregunta abierta codificada suele tener su propia hoja (ej.: `P7_1_CODED`, `P19_CODED`), pero también puede venir todo en una sola hoja con una columna de pregunta.
- En cada hoja hay filas de título/membrete al inicio en posiciones **variables**. Localiza la fila de encabezados buscando una celda cuyo texto sea o contenga `Código`/`Codigo`/`Code`; la tabla de códigos comienza debajo.
- Identifica las columnas: número de código, y una o más columnas de etiqueta. Es habitual el patrón `net1`/`net2` (o `NET`/`Cod`): la etiqueta en la columna de net marca una fila de **NET (categoría agrupadora)** y la etiqueta en la columna siguiente marca un **sub-código**. Puede haber más niveles (`net3`) o ninguno (lista plana): adapta la lectura a lo que encuentres.
- Detecta los **códigos especiales** por su etiqueta (OTROS, NINGUNO, NADA, NO SABE, NO PRECISA, NS/NC…). Suelen ser `94`–`99` pero no siempre: clasifícalos por etiqueta, no solo por rango.
- Ignora filas vacías, celdas de relleno y columnas fantasma (hojas que reportan cientos de columnas pero solo usan las primeras).

**De la base (CSV):**
- Léela con un parser CSV real y `encoding='utf-8-sig'` (frecuentemente hay BOM; si falla, prueba `latin-1`). Las celdas multi-respuesta van entre comillas y contienen comas.
- Las respuestas categóricas suelen almacenarse como conjuntos `{_100}` o `{_100,_101,_200}` (prefijo `_` por código). Verifica el formato real en las primeras filas y adapta el parseo si difiere (ej.: sin llaves, separador `;`, sin prefijo).
- Celda vacía = sin respuesta (no aplica / filtrado): es válida, no un error.
- Identifica la columna que sirve de **identificador de registro** (`Respondent.Serial`, `serial`, `ID`, `record`…) para citar ejemplos.

**Emparejamiento hoja ↔ columna:**
- Empareja cada hoja del LC con su columna en la base **sin distinguir mayúsculas/minúsculas** y tolerando variantes menores (ej.: hoja `P20_CODED` ↔ columna `P20_cODED`; hoja `P7.1` ↔ columna `P7_1_CODED`; sufijos `_CODED`, `_COD`, `_C`).
- Reporta en el informe la tabla de emparejamiento resultante y los elementos sin pareja (ver reglas W4/W5).

## FASE 2 — EXTRACCIÓN

1. **LC:** por hoja/pregunta, lista de códigos con número, etiqueta y tipo (NET, sub-código o especial).
2. **Metadata (si existe):** por variable codificada, su tabla código→etiqueta (normaliza quitando prefijos `_`).
3. **Datos:** por columna emparejada, inventario de códigos distintos usados y frecuencia de cada uno (normaliza: sin prefijo `_`, sin distinguir mayúsculas).

## FASE 3 — REGLAS DE VALIDACIÓN

Clasifica cada hallazgo por severidad:

### ERRORES (deben corregirse)

- **E1 — Código huérfano en datos:** código presente en la base pero **no definido en la hoja correspondiente del LC**. Reporta pregunta, código, frecuencia y hasta 5 registros de ejemplo.
- **E2 — Discrepancia LC ↔ metadata:** código presente en la metadata pero no en el LC, o viceversa (solo si hay metadata). Indica la dirección.
- **E3 — Etiqueta inconsistente:** mismo código con etiquetas materialmente distintas entre LC y metadata (ignora mayúsculas, tildes, espacios y truncamientos evidentes; reporta solo diferencias de significado).
- **E4 — Código duplicado en el LC:** mismo número repetido dentro de una misma hoja/pregunta.
- **E5 — Columna codificada con datos y sin LC:** columna de la base con datos codificados que no tiene hoja/sección en el LC.

### ADVERTENCIAS (revisar, pueden ser válidas)

- **W1 — Código sin menciones:** definido en el LC pero ausente en la base. Habitual, pero debe listarse con su etiqueta.
- **W2 — Sub-código sin su NET / NET sin sub-código:** si el esquema usa NETs (ej. centenas: `_101` implica `_100`), reporta registros donde aparece un sub-código sin su NET, y NETs presentes sin ningún sub-código de su grupo (posible "mención genérica"). Deriva la relación NET↔sub-código de la estructura real del LC, no de una convención fija.
- **W3 — Exclusividad de códigos especiales:** códigos tipo NADA/NINGUNO/NS-NC conviviendo en un mismo registro con códigos sustantivos de la misma pregunta.
- **W4 — Columna sin hoja en LC:** columna codificada de la base sin pareja en el LC y **sin datos** (si tiene datos es E5).
- **W5 — Hoja sin columna en base:** hoja del LC sin columna correspondiente en la base.

### INFORMATIVOS

- **I1 — Cobertura por pregunta:** códigos en LC, códigos usados, % de uso, total de menciones, registros con respuesta vs vacíos.
- **I2 — Frecuencias:** por pregunta, tabla código | etiqueta | tipo | n de menciones, ordenada por grupo y código.

## CONSIDERACIONES TÉCNICAS

- Escribe y ejecuta un **script Python** (ej. `openpyxl` + `csv`) que haga descubrimiento, extracción y cruce; basa el informe en su salida, con números exactos. No valides "a ojo" leyendo los archivos en el chat.
- Si una hoja o columna no encaja en los patrones descritos, documenta lo encontrado y valida lo que sí sea posible; **no inventes estructura ni datos**.
- Si el proyecto trae varios pares LC/base (varias olas, células o versiones), pregunta cuál validar o valida cada par por separado, dejándolo explícito en el informe.

## FORMATO DEL INFORME (entregable)

Informe en Markdown, en español:

1. **Veredicto general:** `APROBADO` (sin errores), `APROBADO CON OBSERVACIONES` (solo advertencias) o `RECHAZADO` (errores E1–E5), con conteo de hallazgos por severidad.
2. **Estructura detectada:** archivos usados, tabla de emparejamiento hoja↔columna, formato de códigos detectado y supuestos aplicados.
3. **Tabla resumen por pregunta:** pregunta | códigos en LC | códigos en base | huérfanos (E1) | sin menciones (W1) | estado.
4. **Detalle de errores:** regla, pregunta, código(s), etiqueta(s), frecuencia y registros de ejemplo.
5. **Detalle de advertencias:** agrupadas por regla.
6. **Anexo informativo:** cobertura (I1) y frecuencias (I2).
