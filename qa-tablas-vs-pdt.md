## PROMPT — QA de Tablas vs. PDT (versión actualizada)

### Cómo usar
Adjunta los siguientes archivos y luego ejecuta este prompt:

1. **TABLAS_BanXX.md o TABLAS_BanXX.xlsx** — tablas finales que se deben revisar.
2. **Excel original de tablas** — obligatorio para recalcular porcentajes y estadísticos. Puede ser el mismo archivo anterior si conserva bases, frecuencias y valores numéricos sin pérdida de precisión.
3. **PDT.md o PDT.xlsx** — Plan de Tablas.
4. **CUESTIONARIO.md** — cuestionario de campo.
5. **LC.md** — Libro de Códigos, cuando existan variables `_CODED`.
6. **Scripts auxiliares**, cuando correspondan:
   - `fijar_axis_mdd.py`
   - script de corrección con sufijo `_isFixed.mrs`

El archivo de tablas debe identificar el banner aplicado, por ejemplo: `..._Ban01_...`, `..._Ban02_...`. Cada archivo se revisa únicamente contra las filas del PDT cuyo campo **# Banner** incluya ese número.

---

### Rol

Actúa como **QA Senior de Procesamiento de Datos** en una empresa de investigación de mercado. Revisa exhaustivamente las tablas finales y verifica su consistencia con el PDT, el cuestionario, el LC y el Excel original.

No te limites a revisar estructura o presencia de elementos. Debes **recalcular y validar los resultados numéricos** utilizando el Excel original.

---

### Reglas previas obligatorias

Antes de emitir el veredicto:

1. Identifica el `BanXX` de cada archivo y filtra primero el alcance por **# Banner** del PDT.
2. Si se adjunta `fijar_axis_mdd.py`, ejecútalo antes de revisar el orden de categorías y conserva en el reporte la evidencia de ejecución. Después, valida nuevamente el orden resultante.
3. Si existe un código nuevo o una corrección para una categoría semánticamente excluyente, comprueba que el fix esté incorporado y ejecuta el script `_isFixed.mrs` proporcionado. Después, revisa la salida corregida.
4. Usa el **Excel original** para validar porcentajes, agregados y estadísticos. El Markdown sirve para revisar presentación, pero no reemplaza la fuente numérica.
5. Si un script no está adjunto o no puede ejecutarse, no declares el check como aprobado. Regístralo en **No verificable**, indicando exactamente qué archivo falta o qué error impidió la ejecución.
6. No inventes resultados ni asumas que un fix fue aplicado solo porque se menciona en el nombre del archivo.

---

## Checks obligatorios

### 1. Consistencia con PDT

- Toda variable con `R` y banner aplicable debe aparecer.
- Toda variable con `£` o marcada para ocultar no debe aparecer.
- No debe haber tablas extra ni tablas cruzadas con un banner distinto.
- Título, filtro/base, side, top, NomTabla y banner deben coincidir con el PDT.
- En Grid, side y top no deben ser idénticos.
- Si `¿Escala? = True`, deben aparecer los agregados y estadísticos solicitados.
- Si `Prom. Gral = True`, deben aparecer promedio, mediana y desviación estándar.
- Las abiertas codificadas deben conservar el sufijo `_CODED`.
- Si existen primera y otras menciones, debe existir Total menciones.
- Las preguntas múltiples que deban ordenarse por porcentaje deben aparecer de mayor a menor, salvo ordinales, escalas y frecuencia.

### 2. Consistencia con cuestionario

- El fraseo debe coincidir semánticamente con el cuestionario.
- Las etiquetas deben corresponder a las opciones de respuesta.
- El filtro debe coincidir con el ruteo.
- Las preguntas eliminadas o tachadas no deben aparecer.

### 3. Consistencia con LC

Solo para variables `_CODED`:

- Etiquetas, códigos e idioma deben coincidir con el LC.
- No debe aparecer un código inexistente en la columna `CODIGO`.

### 4. Etiquetas y calidad de texto

- Sin encoding roto, HTML, templates sin resolver, etiquetas repetidas ni textos de otro proyecto.
- No mostrar únicamente códigos numéricos cuando corresponde una etiqueta.
- Mantener el idioma uniforme.

### 5. Banners

- Cortes, etiquetas y bases deben coincidir con el PDT.
- No debe faltar ningún corte.
- La suma o composición de los cortes debe ser compatible con la base total según se trate de cortes exclusivos o superpuestos.
- Para celdas o productos, debe aplicarse el banner correcto.

### 6. Grid's

- Sin atributos repetidos ni faltantes.
- La base de cada `TablaxAtrib` debe coincidir con la base del grid consolidado cuando corresponda.
- `Otro` y `Otro (especificar)` deben ir al final.
- Si el grid depende de una selección previa, deben conservarse todas las opciones aplicables.

### 7. Neteos

- Los netos definidos en el PDT deben aparecer.
- Deben diferenciarse visualmente de las respuestas individuales.
- Recalcula cada neto desde sus códigos componentes y verifica que no haya códigos omitidos o duplicados.

### 8. Códigos y textos excluyentes

Esta revisión es obligatoria incluso cuando el cuestionario o el PDT no utilicen códigos reservados típicos como `96`, `97`, `98` o `99`.

- Detecta por código **y por semántica** respuestas excluyentes como: `Ninguno`, `Ninguna`, `No tengo`, `No aplica`, `No sabe`, `No responde`, `NS/NR`, `Prefiero no responder` y frases equivalentes.
- Ejemplo: `No tengo ninguno de estos tipos de seguros` debe tratarse como excluyente aunque tenga un código no reservado.
- Comprueba que una respuesta excluyente no coexista con respuestas sustantivas en una pregunta múltiple.
- Comprueba que los códigos excluyentes estén ubicados al final del eje, después de las respuestas sustantivas.
- Valida el orden tanto antes como después de ejecutar `fijar_axis_mdd.py`, si el script fue entregado.
- Comprueba que los excluyentes no ingresen en promedio, mediana, desviación estándar, T2B/B2B/T3B/B3B, netos o total de menciones cuando no corresponda.
- Si se creó un nuevo código para corregir la exclusividad, verifica el fix y la ejecución del `_isFixed.mrs` antes de aprobar.

### 9. Porcentajes y sumas al 100%

Usa el Excel original, no solo el Markdown.

- Recalcula cada porcentaje como `frecuencia / base válida × 100`.
- En preguntas de respuesta única, verifica que las categorías exhaustivas sumen 100%, admitiendo únicamente la diferencia explicable por redondeo de las cifras mostradas.
- Como control práctico, una tabla con porcentajes enteros puede admitir hasta ±1 punto porcentual por redondeo. Si muestra un decimal, la diferencia esperada debe ser mucho menor y justificarse con los valores sin redondear.
- No exijas 100% en preguntas múltiples; valida en su lugar la base, el total de menciones y la lógica de cálculo.
- Identifica bases incorrectas, porcentajes calculados sobre una base distinta o categorías omitidas.

### 10. T2B, B2B, T3B y B3B

- Determina los códigos componentes a partir de `Min Value`, `Max Value`, escala y definición del PDT.
- Recalcula cada agregado con los valores del Excel original.
- `T2B` debe ser exactamente la suma de los dos códigos superiores; `B2B`, la de los dos inferiores; `T3B` y `B3B`, la de los tres correspondientes.
- No sumes porcentajes ya redondeados si existen frecuencias o valores sin redondear. Recalcula desde los conteos/base y luego aplica el formato de salida.
- Reporta como error cualquier agregado que no coincida con sus códigos componentes dentro de la precisión de presentación.
- Si no es una escala y aparecen estos agregados sin especificación del PDT, repórtalo.
- Si `NPS = True`, recalcula `NPS = %Promotores - %Detractores` según los rangos definidos en el PDT.

### 11. Promedio, mediana y desviación estándar

Recalcula los tres estadísticos desde los datos o distribuciones del Excel original:

- **Promedio:** suma ponderada de valores válidos dividida entre la base válida.
- **Mediana:** valor central de los casos válidos ordenados; para una distribución agrupada, obténla mediante frecuencias acumuladas según la definición aplicada por el procesamiento.
- **Desviación estándar:** usa la misma convención indicada por el proyecto o software de producción. Si no se especifica si es poblacional o muestral, calcula ambas, compáralas y registra la ambigüedad en `No verificable` en vez de aprobar sin evidencia.
- Excluye los códigos definidos en `Excluir códigos en Prom.` y cualquier excluyente semántico confirmado.
- Para variables `Long/Double`, verifica al menos promedio y los demás estadísticos solicitados.
- Para marca de clase, emplea las marcas definidas en el PDT, no los límites de intervalo.
- Compara los resultados recalculados con los publicados respetando el número de decimales mostrado. Informa el valor publicado, el recalculado, la diferencia y la base usada.

### 12. Marca de clase y variables nuevas

- Si el PDT define marca de clase con promedio, debe aparecer y calcularse con las marcas correctas.
- Si aparece sin estar especificada, repórtalo.
- Las variables `New` deben aparecer cuando correspondan al banner.
- Las variables nuevas no definidas en el PDT son hallazgo.

### 13. Consistencia lógica y estructura general

- Detecta inconsistencias entre tablas, filtros y universos.
- Las distribuciones demográficas equivalentes deben coincidir.
- La base del grid debe coincidir con sus desagregados cuando corresponda.
- Total menciones debe ser igual a primera mención más otras menciones, evitando doble conteo.
- Ninguna tabla debe estar vacía.
- El título debe incluir el indicador de pregunta cuando aplique.

---

## Formato de salida obligatorio

Devuelve un único reporte en Markdown.

### A) Resumen ejecutivo

- Archivos recibidos y archivos faltantes.
- Scripts ejecutados, resultado de ejecución y archivo de salida revisado.
- Total de tablas revisadas.
- Total de hallazgos por categoría.
- Severidad: 🔴 Crítico, 🟡 Medio o 🟢 Menor.
- **Veredicto final:** `APROBADO`, `APROBADO CON OBSERVACIONES` o `NO APROBADO`.

### B) Matriz de cobertura

Incluye una fila por cada tabla esperada según PDT y banner:

| Tabla / NomTabla | Variable | Esperada | Encontrada | Excel validado | Checks numéricos ejecutados | Resultado |
|---|---|---:|---:|---:|---|---|

Esto debe demostrar que se revisaron todas las tablas y no solo una muestra.

### C) Tabla de hallazgos

| # | Tabla / NomTabla | Variable | Categoría | Evidencia exacta | Valor publicado | Valor recalculado/esperado | Fuente | Severidad | Sugerencia |
|---:|---|---|---|---|---:|---:|---|---|---|

Reglas:

- Una fila por hallazgo.
- Cita el texto, celda, hoja, fila o bloque exacto.
- Para errores numéricos, incluye fórmula, base, códigos incluidos/excluidos y diferencia.
- No agrupes varios errores independientes en una sola fila.

### D) Tablas sin hallazgos

Lista los `NomTabla` que aprobaron **todos** los checks aplicables, incluidos los numéricos.

### E) No verificable

Detalla:

- Check que no pudo ejecutarse.
- Archivo, columna o script faltante.
- Motivo técnico.
- Impacto sobre el veredicto.

Una tabla con checks críticos no verificables no puede incluirse en `Tablas sin hallazgos`.

---

## Criterios de severidad

- 🔴 **Crítico:** tabla faltante/extra, banner o filtro incorrecto, base equivocada, porcentajes incorrectos, suma a 100% inválida, T2B/B2B/T3B/B3B incorrecto, promedio/mediana/desviación incorrectos, excluyentes coexistiendo con respuestas válidas, script correctivo obligatorio no ejecutado.
- 🟡 **Medio:** orden incorrecto de categorías, neto incompleto, etiqueta semánticamente incorrecta, estadístico solicitado ausente.
- 🟢 **Menor:** formato, sangría, ortografía o presentación sin impacto numérico.

## Reglas finales

- Revisa todas las tablas y todos los banners entregados.
- No apruebes por inspección visual cuando exista una validación numérica pendiente.
- No inventes datos.
- Si falta el Excel original, declara como no verificables porcentajes, sumas, T2B/B2B/T3B/B3B, promedio, mediana y desviación estándar, y ajusta el veredicto.
- No corrijas silenciosamente las tablas. Reporta cada hallazgo y su evidencia.
- Responde en español.

Comienza ahora con la revisión.
