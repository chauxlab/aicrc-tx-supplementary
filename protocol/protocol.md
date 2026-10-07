# Protocolo operativo — AICRC_TX

**Título:** Artificial Intelligence in Therapeutic Decision-Making in
Colorectal Cancer: Current Evidence and Perspectives for Precision Oncology

**Tipo de revisión:** Integrativa (Whittemore & Knafl)
**Idioma del manuscrito:** Inglés
**Marco utilizado:** PICO (con desenlaces declarados)
**Fecha de búsqueda:** 2026-09-09 (Canal A PubMed + Canal B Europe PMC)
**review_id:** `SR-AICRC-TX` · **review_slug:** `AICRC_TX`
**Registro externo:** No registrado. PROSPERO no aplica a revisiones
integrativas. OSF opcional. **No se registrará el protocolo** (decisión del usuario, 2026-10-07); `protocol_ref: no registrado` en `review.yaml`.

## Origen del expediente

Expediente nuevo, sin manuscrito ni preprint previo. Búsqueda ejecutada
desde REVISOR: Canal A = PubMed/MEDLINE (conector), Canal B = Europe PMC
(API REST pública EBI). Canal C = rastreo de citas post-cribado de texto
completo.

## Justificación

La oncología del cáncer colorrectal (CCR) ha adoptado con rapidez métodos de
inteligencia artificial / aprendizaje automático para informar decisiones
terapéuticas: predicción de respuesta patológica completa y conducta
órgano-preservadora ("watch-and-wait") en cáncer de recto, predicción de
respuesta y toxicidad a quimioterapia, terapia dirigida (anti-EGFR,
anti-VEGF, BRAF) e inmunoterapia (MSI-H/dMMR), selección de esquema
adyuvante, decisiones quirúrgicas y de secuenciación. Los datos de entrada
abarcan radiómica/imagen (TC, RM, PET), patología digital, genómica y
multiómica, datos clínicos/EHR y, más recientemente, modelos de lenguaje y
sistemas de apoyo a la decisión. Falta una síntesis integrativa que mapee
qué decisiones terapéuticas se han abordado con IA, sobre qué modalidades de
datos, con qué desempeño y qué grado de validación y preparación para la
práctica.

## Pregunta de revisión

En pacientes con cáncer colorrectal, ¿cómo se ha aplicado la inteligencia
artificial / el aprendizaje automático para informar decisiones
terapéuticas, sobre qué modalidades de datos, con qué propósito y desempeño,
y cuál es el estado de su validación clínica?

## PICO

- **P (Población):** Pacientes con cáncer colorrectal (colon y/o recto),
  cualquier estadio, incluida enfermedad metastásica. Se admite
  investigación traslacional con cohortes humanas y datos públicos de
  pacientes (TCGA-COAD/READ, etc.).
- **I (Intervención/Concepto):** Un modelo de inteligencia artificial o
  aprendizaje automático (deep learning, ML clásico, modelos de fundación,
  LLM, sistemas de apoyo a la decisión) cuyo propósito declarado es
  **informar o guiar una decisión terapéutica**: selección de terapia
  sistémica (quimioterapia, anti-EGFR, anti-VEGF, BRAF, inmunoterapia);
  predicción de respuesta o de toxicidad al tratamiento; decisión de
  neoadyuvancia y respuesta patológica completa; conducta órgano-preservadora
  ("watch-and-wait") en recto; decisión o abordaje quirúrgico; radioterapia;
  dosis, secuenciación o desescalada.
- **C (Comparador/Contexto):** Estándar de decisión sin IA (juicio clínico,
  guías, nomogramas convencionales, tumor board) o comparación entre modelos;
  cuando no hay comparador, se describe el rendimiento del modelo.
- **O (Desenlaces declarados):**
  1. Decisión terapéutica abordada y contexto clínico.
  2. Método de IA/ML y datos de entrada (modalidad).
  3. Desempeño reportado (AUC, C-index, HR, sensibilidad/especificidad,
     exactitud, concordancia con el estándar).
  4. Desenlaces del paciente cuando se reporten (respuesta, SLE/SG,
     toxicidad, preservación de órgano).
  5. Nivel de validación: interna (hold-out, validación cruzada), externa
     (cohorte independiente), prospectiva o utilidad clínica demostrada.
  6. Disponibilidad de código/datos y adherencia a guías de reporte
     (TRIPOD-AI, CLAIM, DECIDE-AI, PROBAST).

## Criterios de elegibilidad

### Inclusión

- Estudios **primarios** (retrospectivos o prospectivos) o traslacionales
  con **cohortes humanas o datos de pacientes** con CCR.
- Uso explícito de IA/ML cuyo objetivo es informar o evaluar **una decisión
  terapéutica** (según la definición de Intervención).
- Reporta al menos una métrica de desempeño o de asociación con un desenlace
  clínico/terapéutico.
- Publicado en inglés o español (si el texto es evaluable). Sin límite
  inferior de fecha; se documentará la ventana efectiva.

### Exclusión

- Revisiones, editoriales, comentarios, protocolos y actas sin datos
  primarios (se conservan para contexto y rastreo de referencias).
- IA orientada solo a **tamizaje, detección de pólipos, diagnóstico,
  caracterización de lesiones o estadificación**, sin conexión con una
  decisión de tratamiento.
- IA de **pronóstico puro** sin vínculo explícito con una decisión
  terapéutica (p. ej. firma pronóstica sin implicancia de tratamiento
  declarada). *Nota: si el trabajo enmarca el pronóstico como guía de
  decisión adyuvante/terapéutica, es elegible; caso por caso en cribado.*
- Estudios exclusivamente metodológicos sin aplicación a una cohorte de CCR.
- Estudios exclusivamente preclínicos (líneas celulares, modelos animales)
  sin componente en tejido/datos humanos.
- Tumores no colorrectales; CCR hereditario abordado solo desde consejo
  genético sin decisión terapéutica.

## Ecuaciones de búsqueda

Estructura de bloques: **(CCR) AND (IA/ML) AND (decisión terapéutica)**.
Bloque de población y de IA comunes; el bloque de decisión se ejecutó en
**3 sub-bloques** (por el límite de 20 operadores booleanos del conector
PubMed) unidos por PMID.

Población: `("Colorectal Neoplasms"[MeSH] OR "colorectal cancer"[tiab] OR
"rectal cancer"[tiab] OR "colon cancer"[tiab])`
IA: `("artificial intelligence"[tiab] OR "machine learning"[tiab] OR
"deep learning"[tiab] OR radiomics[tiab] OR "computational pathology"[tiab]
OR "neural network"[tiab])`

### Canal A — MEDLINE (PubMed), ejecutado 2026-09-09

Primera versión (bloque de decisión amplio: `treatment response`,
`neoadjuvant`, `immunotherapy`, `chemotherapy`, `precision oncology`,
`treatment planning`, etc.) → **1.080 únicos**. **Ajuste a pedido del
usuario** para subir precisión: se quitaron los términos sueltos ruidosos y
se exigió señal de decisión más fuerte:

- **Sub-bloque neoadyuvancia / órgano-preservación:**
  `("pathological complete response"[tiab] OR "pathologic complete response"[tiab]
  OR "watch and wait"[tiab] OR "watch-and-wait"[tiab] OR "organ preservation"[tiab]
  OR "neoadjuvant chemoradiotherapy"[tiab] OR "neoadjuvant therapy"[tiab] OR
  "tumor regression grade"[tiab])` → **400**
- **Sub-bloque respuesta / toxicidad al tratamiento:**
  `("response prediction"[tiab] OR "predicting response"[tiab] OR
  "chemotherapy response"[tiab] OR "response to chemotherapy"[tiab] OR
  "response to immunotherapy"[tiab] OR "predict treatment response"[tiab] OR
  "predicting toxicity"[tiab] OR "chemotherapy-induced toxicity"[tiab])` → **195**
- **Sub-bloque selección / decisión terapéutica:**
  `("treatment selection"[tiab] OR "therapy selection"[tiab] OR
  "therapeutic decision"[tiab] OR "treatment decision"[tiab] OR
  "clinical decision support"[tiab] OR "treatment stratification"[tiab] OR
  "guiding treatment"[tiab] OR "personalized treatment"[tiab] OR
  "adjuvant chemotherapy decision"[tiab])` → **332**

Subtotal bruto 927 → **787 únicos tras dedup por PMID**.

### Canal B — Europe PMC (API REST pública EBI), ejecutado 2026-09-09

Traducción a sintaxis `TITLE:`/`ABSTRACT:`, mismos 3 sub-bloques unidos,
`SRC:MED OR SRC:PMC OR SRC:PPR`. 636 registros → tras dedup cruzado por
PMID/DOI con el Canal A y resolución de 2 preprints duplicados: **59
nuevos**. Metadatos del Canal A recuperados también vía Europe PMC
(EXT_ID por lotes); 1 PMID no presente en Europe PMC recuperado vía conector
PubMed.

Corpus RIS combinado:
`searches/ris/AICRC_TX_pubmed_europepmc_2026-09-09.ris`. Campo `N1` marca
`source_channel: A|B`.

### Canal C — "identificados por otros métodos"

Rastreo de citas (hacia atrás y adelante) de los estudios incluidos y de
las revisiones recuperadas, tras el cribado de texto completo, más
literatura que aporte el usuario. Se declara aparte en PRISMA.

### Bases sin conector ni API abierta

Embase, Web of Science, Scopus, IEEE Xplore, Cochrane CENTRAL: requieren
acceso institucional no disponible. Omisión aceptable para revisión
integrativa; se documenta como limitación.

## Contabilidad PRISMA (n reales — 2026-09-09)

| Etapa | n |
|---|---|
| Canal A PubMed — sub-bloque neoadyuvancia | 400 |
| Canal A PubMed — sub-bloque respuesta/toxicidad | 195 |
| Canal A PubMed — sub-bloque selección/decisión | 332 |
| Canal A — subtotal bruto | 927 |
| Canal A — únicos tras dedup por PMID | 787 |
| Canal B Europe PMC — bruto | 636 |
| Canal B — nuevos tras dedup cruzado + resolución de preprints duplicados | 59 |
| **Corpus combinado ingresado a SQLite** | **846** |
| Canal C (rastreo de citas, post-cribado FT) | pendiente |

Registros: 846 (`REC-AICRCTX-000001`…`000848`, con 2 huecos por
deduplicación: 000818, 000844; 839 con abstract), verificado en intake
(ver `logs/workflow-log.md`).

## Deduplicación e intake

- Deduplicación cruzada por DOI/PMID antes del conteo final.
- Intake a SQLite con `python3 scripts/review_intake_ris.py --review
  reviews/AICRC_TX` (prefijo `REC-AICRCTX-000001`…).

## Cribado

- Título/abstract doble e independiente: R1 = `REV-ALCIDES`, R2 =
  `REV-PAOLA`. Consenso documentado; kappa reportado.
- Texto completo: doble, con motivos de exclusión controlados.
- Catálogo RIS del corpus `Included` para Paperpile al pasar de consenso a
  recuperación de texto completo (ver `manual/full-text-catalog-ris.md`).

### Preprints

Se retienen para cribado. Regla de sustitución: si se identifica una versión
publicada, se sustituye el preprint y se documenta en `decision-log.md`.

## Evaluación crítica (appraisal)

- **MMAT v.2018** como instrumento primario (heterogeneidad de diseños).
- Complemento dirigido para modelos predictivos: dominios **PROBAST** /
  **TRIPOD-AI**, registrado por estudio.
- **Proceso (2026-09-28):** escala por R1 (`REV-ALCIDES`) con codebook
  congelado; verificación independiente por R2 (`REV-PAOLA`) sobre una
  **muestra estratificada ~10–15 %** del corpus Included FT, con acuerdo
  reportado. No se exige doble appraisal de todos los incluidos. Detalle
  en `risk_of_bias/AICRC_TX_appraisal_codebook.md`.

## Extracción

Formulario ancho en `extraction/`. Campos mínimos: localización (colon /
recto / ambos), estadio, n, fuente de datos, decisión terapéutica abordada,
modalidad de dato, tarea/algoritmo de IA, features de entrada, comparador,
métrica y valor de desempeño, desenlaces del paciente, validación
(interna/externa/prospectiva/utilidad), disponibilidad de código y datos,
guía de reporte declarada, financiamiento y conflictos.

## Síntesis

Síntesis integrativa narrativa (Whittemore & Knafl): reducción, despliegue,
comparación y conclusiones, estratificada por **decisión terapéutica** y por
**modalidad de dato**. Sin metaanálisis previsto (heterogeneidad de
desenlaces y métricas). Tablas de mapeo decisión × modalidad × nivel de
validación.

## Handoff

Al cerrar corpus FT + appraisal + extracción:
`python3 scripts/export_handoff_sesion2.py --review reviews/AICRC_TX`
(ver `manual/handoff-sesion2.md`). REDACTOR Sesión 2 redacta en inglés.

## Estado

Protocolo aprobado (2026-09-09). Búsqueda en bases **cerrada**: Canales A
(PubMed) y B (Europe PMC) ejecutados e ingresados a SQLite — **846
registros combinados** (`REC-AICRCTX-000001`…`000848`, 2 huecos por dedup).
**Siguiente:** cribado título/abstract doble R1/R2 desde
`initial_screening`. Canal C (rastreo de citas) tras el cribado de texto
completo.
