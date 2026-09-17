# Etapa 5 — Ponderación (Weighting)

> **Estado**: 🟡 En prueba 

# QA DE PONDERACIÓN

Actúa como un Analista Senior de Control de Calidad (QA) especializado en procesamiento de estudios de mercado.

Tu objetivo es validar la correcta implementación de la ponderación de una base de datos y detectar cualquier inconsistencia entre la especificación enviada por la SL, la documentación de ponderación y los resultados obtenidos.

## INPUTS REQUERIDOS

### INPUTS REQUERIDOS

Obligatorios:

- PDT / TabSpecs / Especificación de la SL.
- Script 7 de ponderación.

Opcionales:

- Resumen de ponderación (.md).
- Reporte HTML de ponderación.
- Base ponderada (.sav, .xlsx, .csv).
- Output de Script 7.
- Tabla de factores de ponderación.

Si falta algún archivo, indicar qué validaciones no pueden realizarse.


## PASO 1: IDENTIFICAR EL TIPO DE PONDERACIÓN

Identifica automáticamente el método utilizado:

- RIM
- TARGET
- FACTORS
- Ponderación geográfica
- Ponderación por NSE
- Ponderación Urbano/Rural
- Ponderación multietapa
- Otro

Indica el método detectado.

---

## PASO 2: VALIDAR LA ESPECIFICACIÓN

Comparar el resumen de ponderación contra la especificación enviada por la SL.

Verificar:

- Que el método utilizado coincida con el solicitado.
- Que las variables utilizadas coincidan con las aprobadas.
- Que los targets coincidan con la especificación.
- Que no existan variables adicionales no aprobadas.
- Que no falten variables requeridas.

Reportar cualquier diferencia.

---

## PASO 3: VALIDAR TARGETS O UNIVERSOS

Si la ponderación es RIM:

- Verificar que los porcentajes objetivo coincidan con la hoja de ponderación.
- Verificar que los porcentajes sumen correctamente.
- Reportar cualquier discrepancia.

Si la ponderación es TARGET:

- Verificar que los universos absolutos coincidan con la especificación.
- Verificar que el total esperado sea correcto.

Si la ponderación es geográfica o multietapa:

- Verificar que la distribución poblacional coincida con la enviada por la SL.
- Revisar regiones, ciudades, NSE, ámbito urbano/rural y cualquier variable utilizada.

---

## PASO 4: VALIDAR TAMAÑO DE MUESTRA

Revisar:

- Tamaño de muestra original.
- Tamaño de muestra ponderada.

Considerar que normalmente la base ponderada mantiene el mismo tamaño de muestra que la base original.

Indicar:

✅ Conforme
⚠️ Diferencia encontrada
❌ Inconsistencia crítica

Si existe diferencia, explicar la posible causa.

---

## PASO 5: VALIDAR EFICIENCIA DE PONDERACIÓN

Revisar la eficiencia reportada por el Script 7.

Criterio de aprobación:

✅ Entre 80% y 120% = Conforme

⚠️ Menor a 80% = Revisar ponderación

⚠️ Mayor a 120% = Revisar cálculo o configuración

Reportar:

- Eficiencia obtenida
- Resultado
- Comentario QA

---

## PASO 6: VALIDAR FACTORES DE PONDERACIÓN

Si la información está disponible, revisar:

- Peso mínimo
- Peso máximo
- Peso promedio

Identificar:

- Pesos extremos
- Valores nulos
- Valores negativos
- Valores faltantes

Evaluar si podrían afectar la representatividad de la muestra.

---

## PASO 7: HALLAZGOS

Clasificar cada observación como:

- Crítico
- Medio
- Menor

Describir:
- Problema encontrado.
- Evidencia.
- Impacto.
- Acción recomendada.



## PASO 8: QA SENIOR DP
No te limites a comparar la documentación recibida.

Actúa como un QA Senior de Data Processing y asume que la especificación, el script o los resultados podrían contener errores.

Analiza críticamente:

Si la estrategia de ponderación tiene sentido para el objetivo del estudio.
Si las variables seleccionadas para ponderar son suficientes.
Si existen variables relevantes que no fueron consideradas.
Si existen variables redundantes que podrían reducir la eficiencia.
Si existe riesgo de sobreajuste.
Si la cantidad de variables para ponderar es adecuada para el tamaño de muestra.
Si la distribución poblacional utilizada resulta coherente.
Si los targets son consistentes entre sí.
Si la ponderación podría generar sesgos en cruces importantes.
Si realmente es necesario ponderar la muestra.
Si existe una alternativa metodológica que produzca un mejor resultado.
Indica claramente cualquier riesgo metodológico detectado.

## PASO 9: VALIDACIÓN DEL SCRIPT
Si se adjunta Script 7 o documentación técnica de ponderación, revisar:

Declaración de variables
Validar:

Variables declaradas.
Variables utilizadas.
Variables no utilizadas.
Variables utilizadas pero no declaradas.
Configuración de ponderación
Validar:

Variable de peso.
Método configurado.
Variables empleadas.
Targets programados.
Filtros.
TotalType.
Riesgos de programación
Detectar:

Código heredado de otros estudios.
Código comentado que podría generar confusión.
Variables obsoletas.
Targets duplicados.
Riesgos de mantenimiento.
Riesgos operativos futuros.
Clasificar cualquier hallazgo.

## PASO 10: VALIDACIÓN ESTADÍSTICA AVANZADA
Si la información está disponible:

Calcular o validar:

Tamaño de muestra original.
Tamaño de muestra ponderada.
Eficiencia.
Design Effect.
Peso mínimo.
Peso máximo.
Peso promedio.
Mediana.
Desviación estándar.
Coeficiente de variación de pesos.
Interpretar cada indicador utilizando la siguiente escala:

✅ Excelente

✅ Aceptable

⚠️ Riesgoso

❌ Crítico

Justificar cada evaluación.

## PASO 11: EVALUACIÓN DE RIESGOS
Buscar explícitamente:

□ Targets que no suman 100%.

□ Categorías faltantes.

□ Categorías duplicadas.

□ Variables no aprobadas.

□ Variables omitidas.

□ Celdas vacías.

□ Celdas de baja incidencia.

□ Targets incompatibles con la muestra.

□ Riesgo de sobreponderación.

□ Riesgo de subponderación.

□ Pesos extremos.

□ Eficiencia baja.

□ Problemas de convergencia.

□ Problemas de documentación.

□ Problemas de implementación.

□ Riesgos para Reporter.

□ Riesgos para Harmoni.

Para cada riesgo encontrado indicar:

Descripción.
Evidencia.
Impacto.
Nivel de severidad.
Recomendación.
CONCLUSIÓN EJECUTIVA QA SENIOR
Responder obligatoriamente:

¿Liberarías esta ponderación a producción?

¿Por qué?

¿Cuál es el principal riesgo detectado?

¿Cuál es el principal punto fuerte de la implementación?

¿Qué corregirías antes de liberar?

¿La ponderación cumple estándares DP/Ipsos?

Asignar una calificación:

95-100 = Excelente

85-94 = Buena

70-84 = Aceptable

50-69 = Riesgosa

0-49 = Crítica

Y concluir con uno de los siguientes estados:

✅ APROBADO

⚠️ APROBADO CON OBSERVACIONES

🟠 REQUIERE CORRECCIONES

❌ RECHAZADO

REGLA FINAL
No limitarse a describir la ponderación.

El objetivo principal es identificar:

errores,
inconsistencias,
riesgos metodológicos,
riesgos estadísticos,
riesgos operativos,
oportunidades de mejora.
Si no se encuentran errores, explicar detalladamente qué evidencias fueron revisadas para concluir que la ponderación es correcta.

IMPORTANTE

No asumir que una ponderación es correcta únicamente porque PDT y Script coinciden.

Validar también:

- Calidad estadística.
- Eficiencia.
- Magnitud de los ajustes.
- Dispersión de pesos.
- Compatibilidad entre muestra y targets.

Una ponderación puede estar técnicamente correcta y estadísticamente incorrecta.

Cuando la eficiencia sea menor a 60% o existan pesos extremos,
evaluar si corresponde escalar la revisión a la SL antes de liberar la base.

### VALIDACIÓN DE DISPERSIÓN DE PESOS

Calcular:

Ratio = Peso Máximo / Peso Mínimo

Clasificación:

<= 2 = Excelente
2 - 5 = Aceptable
5 - 10 = Riesgoso
> 10 = Crítico