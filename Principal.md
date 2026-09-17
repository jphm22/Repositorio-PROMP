# 🧠 Prompts DP — Biblioteca de prompts del equipo de Data Processing

Repositorio compartido de **prompts LLM para QA y validación del proceso de Data Processing** (Ipsos), organizado por etapas del proceso. La idea es que el equipo de programación lo vaya **alimentando**: cada etapa tiene su carpeta y los huecos están marcados.

## Contenido

- [Estado de cobertura](#estado-de-cobertura)
- [Estructura del repositorio](#estructura-del-repositorio)
- [Prompts disponibles](#prompts-disponibles)
- [Cómo usar un prompt](#cómo-usar-un-prompt)
- [Cómo alimentar el repositorio](#cómo-alimentar-el-repositorio)
- [Roadmap sugerido](#roadmap-sugerido)

## Estado de cobertura

De los **86 contenidos** del proceso DP: **28 cubiertos**, **15 parciales**, **43 pendientes**.
Detalle completo, contenido por contenido: **[docs/cobertura-temario-dpt.md](docs/cobertura-temario-dpt.md)**.

## Estructura del repositorio

```
.
├── README.md                      ← este archivo
├── docs/
│   └── cobertura-temario-dpt.md   ← matriz de cobertura proceso vs prompts
└── prompts/                       ← LOS PROMPTS, una carpeta por etapa
    ├── 00-captura-ifield/         ← pre-DP: auditoría de scripting de campo
    ├── 01-pdt/                    🟡 prueba
    ├── 02-validacion-bd/          🟢
    ├── 03-variables/              🟢
    ├── 04-codificacion/           🟡
    ├── 05-ponderacion/            🟡 prueba
    ├── 06-union-bases/            🔴 pendiente
    ├── 07-bases-datos/            🟡
    ├── 08-harmoni/                🟢
    ├── 09-tablas-excel/           🟡
    └── 10-spss/                   🔴 pendiente
```

La numeración de `prompts/` sigue el orden del proceso DP; `00-captura-ifield` es la etapa previa (programación de campo). Una carpeta con solo `README.md` = etapa pendiente de alimentar.

**Leyenda de estado (por etapa):** 🟢 con prompts activos · 🟡 cobertura parcial · 🔴 pendiente. Es una foto agregada de la etapa — para el detalle contenido por contenido (con su propia leyenda ✅/🟡/❌) revisa la [matriz de cobertura](docs/cobertura-temario-dpt.md).

## Prompts disponibles

| Prompt | Etapa | Qué hace | Entradas típicas |
|---|---|---|---|
| [auditor-scripting-ifield.md](prompts/00-captura-ifield/auditor-scripting-ifield.md) | 0 — Captura (pre-DP) | Audita cuestionario vs estructura MDD vs código OSM/JavaScript de iField. Solo reporta inconsistencias verificables (v14) | `Cuestionario.docx`, `Estructura.docx`, `Codigo.docx` |
| [generar-script-validacion-dms.md](prompts/02-validacion-bd/generar-script-validacion-dms.md) | 2 — Validación de BD | **Genera** el script de validación / data cleaning en mrScriptBasic (filtros, exclusivos, rangos, sumas, loops N-D) con regla estricta de no-inferencia | `MDD.md`, `Cuestionario.md`, script previo `.dms` (opcional) |
| [qa-variables-pdt-vs-scripts-dms.md](prompts/03-variables/qa-variables-pdt-vs-scripts-dms.md) | 3 — Variables | Audita que **todas** las variables pedidas en el PDT estén declaradas y llenadas correctamente en los scripts DMS | `PDT.md`, scripts `.dms`, metadata MDD (opcional) |
| [qa-libro-codigos-vs-base.md](prompts/04-codificacion/qa-libro-codigos-vs-base.md) | 4 — Codificación | Audita consistencia entre Libro de Códigos y base de datos (descubrimiento dinámico de estructura) | `LC.xlsx`, `BASE.csv`, metadata `.md` (opcional) |
| [qa-entregable-bvc-sav.md](prompts/07-bases-datos/qa-entregable-bvc-sav.md) | 7 — Bases de datos | Valida integridad del entregable Base BVC SAV: inconsistencias reales vs excepciones, frecuencias, etiquetas, recodes | `Cuestionario.md`, `BaseSav.md`, `Frecuencias.md`, `Inconsistencias.md` |
| [qa-dataspec-vs-base.md](prompts/07-bases-datos/qa-dataspec-vs-base.md) | 7 — Bases de datos | Valida que el DataSpec del cliente sea viable contra la base y que la plantilla DataSpec esté bien parametrizada | `DataSpec.md`, `base.md/txt`, `Plantilla_Dataspec.md` |
| [qa-proyecto-harmoni.md](prompts/08-harmoni/qa-proyecto-harmoni.md) | 8 — Harmoni | Audita la configuración completa de un proyecto en Harmoni: 17 checks (accesos, árbol, grids, measures, NPS, banners, ponderación, neteos, orden…) | `PDT.xlsm`, cuestionarios `.docx`, `BASE.md`/`BASE.xlsx`, `EstructuraHarmoni.xlsx`, `PROMEDIOS.xlsx`, `LDC.xlsx`, usuarios, `WTVAR` |
| [qa-tablas-vs-pdt.md](prompts/09-tablas-excel/qa-tablas-vs-pdt.md) | 9 — Tablas Excel | QA de tablas cross-tab finales contra PDT, cuestionario y LC, banner por banner (`Ban01`, `Ban02`…) | `TABLAS_BanXX.md`, `PDT.md`, `CUESTIONARIO.md`, `LC.md` |

## Cómo usar un prompt

1. **Elige el prompt** de la etapa que corresponda (tabla de arriba) y ábrelo.
2. **Cópialo completo** como instrucciones / system prompt de tu asistente LLM (Claude, ChatGPT, Gemini…): ya trae rol, entradas esperadas, reglas anti-alucinación y formato de salida definidos, no hace falta agregar nada.
3. **Adjunta las entradas** que pide ese prompt (columna "Entradas típicas"). Respeta el formato que cada uno indica: algunos piden el documento nativo (`.docx`, `.xlsx`), otros piden una versión en texto/Markdown (`MDD.md`, `Cuestionario.md`) — usar el formato equivocado es la causa más común de que el modelo alucine estructura.
4. **Ejecuta y revisa el reporte con ojo crítico**: los prompts exigen evidencia (archivo, variable, valor exacto) para cada hallazgo y prohíben inferir o suponer. Un hallazgo sin esa evidencia es sospechoso, repórtalo como posible falso positivo antes de actuar sobre él.

> Son prompts agnósticos de herramienta: no dependen de una API ni de un proveedor de LLM en particular, solo de una ventana de contexto suficiente para los documentos que adjuntes.

## Cómo alimentar el repositorio

1. **Ubica la etapa**: cada prompt va en `prompts/NN-etapa/`. Si dudas, revisa la [matriz de cobertura](docs/cobertura-temario-dpt.md) — ahí está el detalle de qué falta por etapa.
2. **Nombra en kebab-case** con prefijo por tipo:
   - `qa-*` → prompts que auditan/validan un entregable
   - `generar-*` → prompts que producen un artefacto (script, PDT, etc.)
   - `auditor-*` → auditorías amplias multi-documento
3. **Formato del prompt** (patrón que ya siguen los existentes):
   - **Rol** (quién es el agente), **Entradas** (archivos y cómo identificarlos), **Reglas** (anti-alucinación / no-inferencia / no falsos positivos), **Formato de salida** (reporte con evidencia).
   - Escríbelo **genérico y reutilizable**: los nombres de archivos y estructuras deben descubrirse dinámicamente, no hardcodearse por proyecto.
4. **Actualiza** la tabla de este README y marca el contenido en [docs/cobertura-temario-dpt.md](docs/cobertura-temario-dpt.md).

> ⚠️ **Confidencialidad**: los materiales de estudios reales (bases, cuestionarios, PDT, MDD/DDF, SAV) **no se versionan en este repositorio** — se mantienen en local. El `.gitignore` bloquea los formatos de datos habituales por si acaso.

## Roadmap sugerido

Ordenado por retorno (contenidos que destaparía cada uno):

1. **QA / generación de PDT** → `prompts/01-pdt/` — el PDT es insumo de todos los demás QA y nadie lo valida.
2. **Comparador de estructuras ola vs ola** → `prompts/02-validacion-bd/` — dos `BASE.md` (MDD actual vs anterior) → reporte de variables nuevas/eliminadas/modificadas.
3. **QA de ponderación** → `prompts/05-ponderacion/`.
4. **QA de unión de bases (post-merge)** → `prompts/06-union-bases/`.
5. **QA de migraciones MDD/DDF ↔ SAV/XLS** → `prompts/07-bases-datos/` — comparar casos, variables y categorías entre origen y destino.
6. **QA de muestra** → `prompts/02-validacion-bd/` — distribución de la muestra vs cuotas/diseño.
