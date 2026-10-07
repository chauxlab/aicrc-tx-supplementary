# Appraisal codebook — AICRC_TX

Instrumentos del protocolo (`protocol/protocol.md`):

1. **MMAT v.2018** — appraisal primario (heterogeneidad de diseños).
2. **PROBAST** (dominios) + **TRIPOD-AI** (adherencia de reporte, forma
   reducida) — complemento **solo** cuando el estudio desarrolla o valida
   un modelo predictivo / firma / clasificador con desenlace terapéutico
   o clínico.

Cohorte: los **227** `Included` del consenso FT
(`screening/conflicts/2026-09-28__consenso-FT-final-271.csv`).

Plantilla operativa: CSV ancho en este directorio. Importación a SQLite
(`risk_of_bias`) al cerrar el appraisal.

Proceso aprobado 2026-09-28: escala R1 completa + verificación R2 por
**muestreo estratificado (~10–15 %)**, no doble ciego de los 227. El
cribado T/A y FT ya fue doble e independiente.

---

## 1. MMAT v.2018

Misma lógica que `reviews/AIBIO_GU/risk_of_bias/AIBIO_GU_appraisal_codebook.md`
y `reviews/HPV_PSCC/risk_of_bias/HPV_PSCC_mmat_codebook.md`.

### Categorías (`mmat_category`)

| Code | When |
|---|---|
| `qualitative` | Diseños cualitativos |
| `rct` | Ensayos aleatorizados (o análisis secundario de RCT) |
| `non_randomized` | Cohorte / casos-controles / comparativo no aleatorizado |
| `descriptive` | Serie de casos / prevalencia / descriptivo de un brazo |
| `mixed_methods` | Métodos mixtos |
| `NA` | No debería quedar en Included FT; si aparece → documentar |

### Screening (empíricos)

- `screen_clear_question`: `yes` \| `no`
- `screen_data_address_question`: `yes` \| `no`

Si alguno es `no` → no completar `c1`–`c5`; `mmat_overall=cannot_appraise`.

### Criterios `c1`–`c5`

Juicios: `yes` \| `no` \| `cant_tell`.  
Racional breve en `rationale_c1`…`rationale_c5`.  
Significado por categoría: Hong et al., MMAT 2018.

### Overall MMAT (`mmat_overall`)

`high` \| `moderate` \| `low` \| `cannot_appraise` \| `NA`

Heurística **mecánica** por conteo de `c1`–`c5` (regla congelada en AIBIO_GU
tras inconsistencia entre lotes; no ajustar a mano):

- `high`: 0 criterios `no`/`cant_tell` (todos `yes`)
- `moderate`: exactamente 1 criterio `no`/`cant_tell`
- `low`: 2 o más criterios `no`/`cant_tell`
- `cannot_appraise`: falló screening o texto insuficiente

`appraisal_note` documenta el razonamiento cualitativo; `mmat_overall` se
deriva siempre del conteo.

---

## 2. ¿Aplica PROBAST / TRIPOD-AI?

`applies_prediction_appraisal`: `yes` \| `no`

Marcar **`yes`** si el paper:

- desarrolla, valida o actualiza un **modelo predictivo** orientado a una
  decisión terapéutica (respuesta a tratamiento, pCR / watch-and-wait,
  toxicidad, selección de esquema, biomarcador accionable MSI/KRAS, etc.),
  **o**
- reporta una **firma/score** usada como predictor de un desenlace clínico
  o terapéutico con métricas tipo AUC / C-index / sensibilidad-especificidad
  / calibración, **o**
- compara un LLM / sistema de apoyo a la decisión con decisiones reales de
  tratamiento (p.ej. tumor board) con métricas de concordancia.

Marcar **`no`** (dejar PROBAST/TRIPOD-AI vacíos o `NA`) si es:

- descubrimiento puramente asociativo sin modelo predictivo formal,
- solo selección de features / ranking sin evaluación predictiva,
- métodos o pipeline técnico sin validación predictiva clínica.

Ante duda → `yes` y documentar en `appraisal_note`.

En este corpus se espera `yes` en casi todos los Included FT.

---

## 3. PROBAST (dominios dirigidos)

Juicios por dominio: `low` \| `high` \| `unclear`  
(equivalente PROBAST: bajo / alto / poco claro riesgo de sesgo).

| Columna | Dominio |
|---|---|
| `probast_participants` | Participantes / fuente de datos / elegibilidad |
| `probast_predictors` | Predictores (definición, medición, disponibilidad al momento de predicción) |
| `probast_outcome` | Desenlace (definición, determinación, ceguera relativa a predictores) |
| `probast_analysis` | Análisis (eventos por variable, missing, overfitting, validación) |
| `probast_overall_rob` | Juicio global de riesgo de sesgo del modelo |
| `probast_overall_applicability` | Aplicabilidad al PCC de AICRC_TX (ver abajo) |

Heurística de `probast_overall_rob`:

- `low` si **todos** los dominios 1–4 son `low`
- `high` si **algún** dominio es `high`
- `unclear` en cualquier otro caso (incluye mezclas con `unclear`)

### Aplicabilidad (`probast_overall_applicability`)

Juicio: `low` concern \| `high` concern \| `unclear`.

Pregunta: ¿el modelo, tal como se desarrolló/validó, es aplicable a la
pregunta de esta revisión — **IA que informa una decisión terapéutica en
CCR**?

Señales de `high` concern (anotar en `probast_rationale` /
`appraisal_note`; **no** reclasifican elegibilidad del cribado):

- desenlace solo pronóstico sin nexo terapéutico analizado (no debería
  haber llegado a Included; si aparece, concern alto),
- cohorte mixta multi-cáncer sin resultados de CCR separables en el
  appraisal del modelo,
- predictores no disponibles en el momento de la decisión clínica que el
  paper declara informar,
- validación solo in silico / líneas celulares como evidencia principal
  del desempeño terapéutico.

---

## 4. TRIPOD-AI (reporte reducido)

No es el checklist completo. Cuatro señales de reporte:

| Columna | Pregunta (yes / partial / no / NA) |
|---|---|
| `tripod_ai_data` | ¿Fuente de datos, elegibilidad y handling de missing claros? |
| `tripod_ai_model` | ¿Arquitectura/algoritmo, features y entrenamiento/tuning descritos? |
| `tripod_ai_validation` | ¿Validación interna y/o externa descrita (método + métricas)? |
| `tripod_ai_transparency` | ¿Código y/o datos disponibles, o justificación de no disponibilidad? |
| `tripod_ai_overall` | `adequate` (≥3 yes) \| `partial` (1–2 yes) \| `inadequate` (0 yes) \| `NA` |

---

## 5. Proceso operativo (aprobado 2026-09-28)

1. **Pilot (n=20):** estratificado por eje terapéutico (neoadyuvancia/pCR,
   biomarcador accionable, toxicidad/cirugía, metastásico/sistémico,
   LLM/DSS, otros). Calibrar y congelar este codebook.
2. **Escala R1:** lotes paralelos sobre los 207 restantes (`REV-ALCIDES`),
   con codebook congelado. `pilot_batch=batch-01`…
3. **Verificación R2:** muestra estratificada **~10–15 %** del corpus
   Included (~25–35 estudios), ciega respecto a los juicios R1 de esa
   muestra. Resolver discrepancias por consenso documentado; reportar
   acuerdo. No se exige doble appraisal de los 227.
4. Appraisal asistido por PDF (métodos/resultados). Marcar
   `appraisal_note` si el juicio es fronterizo.

### Calibración congelada (piloto n=20, 2026-09-28)

Ver `AICRC_TX_appraisal_PILOT_SUMMARY.md`. Resumen:

- `applies_prediction_appraisal=yes` en 20/20; no endurecer el umbral.
- `mmat_overall=high` raro pero real (2/20: 000318, 000476); default
  `low`/`moderate`. Regla mecánica de conteo sin excepciones.
- PROBAST overall `high` en 14/20 (Analysis casi siempre el dominio
  débil); no recalibrar a la baja solo por frecuencia.
- LLM/DSS: `yes` a PROBAST/TRIPOD si hay concordancia o DSS; 000613
  (implementación LASSO) puede tener PROBAST `low` y MMAT `low`.
- Aplicabilidad: mezcla terapéutica o multi-cáncer → `unclear`/`high
  concern` + nota; no reclasificar elegibilidad FT.
- PDF con cuerpo en chino: appraisear si abstract/methods en inglés
  bastan; anotar en `appraisal_note`.

---

## 6. Columnas del CSV

```
record_id, study_label, title, year, journal, doi,
therapeutic_axis,
mmat_category, screen_clear_question, screen_data_address_question,
c1, c2, c3, c4, c5,
rationale_c1, rationale_c2, rationale_c3, rationale_c4, rationale_c5,
mmat_overall, criteria_note,
applies_prediction_appraisal,
probast_participants, probast_predictors, probast_outcome, probast_analysis,
probast_overall_rob, probast_overall_applicability, probast_rationale,
tripod_ai_data, tripod_ai_model, tripod_ai_validation, tripod_ai_transparency,
tripod_ai_overall,
reviewer_id, assessed_at, appraisal_note, pilot_batch
```

- `therapeutic_axis` (contexto de estratificación, no un juicio):
  `neoadj_pcr_ww` \| `biomarker_actionable` \| `tox_surg` \|
  `metastatic_systemic` \| `llm_dss` \| `adjuvant_risk` \| `other`
- `pilot_batch`: `pilot` \| vacío o `batch-01`… (escala) \| `r2_verify`
- Campos de juicio vacíos = pendiente.
