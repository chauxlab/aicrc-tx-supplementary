# Extraction codebook — AICRC_TX (v1.0 FROZEN 2026-10-07)

Wide-form CSV, una fila por `record_id` FT Included (n=227). Valores y notas
en inglés (idioma del manuscrito). `NR` = not reported; `NA` = not
applicable by design (p. ej. datos públicos sin contexto clínico).
Revisión: *AI in Therapeutic Decision-Making in Colorectal Cancer*
(`integrative_review`, `SR-AICRC-TX`). Campos mínimos de `protocol/protocol.md`
§Extracción; síntesis prevista: mapa **decisión terapéutica × modalidad de dato
× nivel de validación** (Whittemore & Knafl, sin metaanálisis).

Cohorte de entrada: `AICRC_TX_extraction_cohort_2026-10-07.csv` (con `pdf_path`
y appraisal cerrado como contexto; **no se re-juzga el appraisal aquí**).

## Diseño del proceso (decidido 2026-10-07)

- **Un único extractor** (decisión del usuario, por plazo). Extracción asistida
  por IA: subagentes de Claude bajo `extractor_id=REV-ALCIDES`, PDF completo.
- Compensación del rigor: (1) piloto de 12 estratificado por eje → este codebook
  v1.0 congelado con 17 cambios (ver `logs/workflow-log.md`); (2) localización
  de la fuente en los campos numéricos clave; (3) validación mecánica
  (`scripts/validate_aicrc_tx_extraction.py`); (4) **verificación independiente
  ciega de una muestra aleatoria del 10 %** con acuerdo por campo;
  (5) `needs_review` resuelto o declarado antes de cerrar.
- Los 12 del piloto se re-extraen con v1.0 junto con el resto (el piloto solo calibró).

## Core rules

1. Extraer solo lo que el paper reporta; si no está, `NR`. Codificar a un
   vocabulario controlado es la única inferencia permitida; justificarla en
   `extraction_note` si no es obvia. No usar conocimiento externo al PDF. Se
   permiten N derivados (p. ej. 25 % de 442) con `n_source` = `derived: 25% of 442`.
2. **Modelo principal**: el declarado por los autores (el que ancla título/resumen/
   conclusión). Si hay varios co-primarios o ninguno ganador: (a) el de la
   conclusión; (b) el de mejor AUC en la cohorte más exigente; (c) si persiste,
   `needs_review=yes` y registrar la elección en `extraction_note`. Los demás
   van en `other_models_outcomes`. Una fila por registro.
3. Números planos en columnas numéricas (sin `%` ni unidades; porcentajes 0–100).
4. Desempeño del modelo principal en la **cohorte más exigente** que el paper
   reporta (prospectiva > externa > temporal > test interno > CV > train),
   indicada en `performance_cohort`; las demás cohortes en `performance_value`
   separadas por `;`. `primary_metric_value` (+ IC) es el número de la métrica
   titular en esa cohorte; en tareas multiclase, la métrica de la clase/objetivo
   principal; el resto en `performance_value`.
5. **Validación**: `validation_level` es el nivel más alto alcanzado por la
   salida del modelo que el paper reclama clínicamente, según lo que se hizo,
   no la etiqueta del paper (un split aleatorio no es externo). Si la cohorte
   independiente carece de la etiqueta de referencia y solo se probó asociación
   con desenlaces, codificar el nivel, poner `validation_basis=outcome_association`
   y fijar `performance_cohort` en la cohorte que realmente sostiene la métrica.
   `external_multicenter` exige ≥2 sitios externos.
6. **N**: `n_analytic` = total de unidades del estudio (desarrollo + validación);
   `n_performance` = N que sostiene `primary_metric_value`; `n_train`/`n_test` =
   enteros o `NR`/`NA` (`n_test` cubre test interno y externo; el tipo lo da
   `performance_cohort`/`validation_level`).
7. `stage_context`: codificar la **población en que se desarrolló el modelo**, no
   la que la discusión apunta. Datos públicos sin contexto clínico → `NA`.
8. `therapeutic_decision`: ante dos opciones, codificar por el tratamiento que el
   modelo predice (ICI → `immunotherapy_msi_selection`; quimiorradioterapia →
   `neoadjuvant_response`; elección por clase de fármaco →
   `systemic_therapy_selection_response`; por biomarcador definido →
   `targeted_therapy_biomarker`). Estudios de descubrimiento sin decisión
   operable → `discovery_no_decision`.
9. `data_modality`: `multimodal` solo si ≥2 modalidades son entradas del modelo;
   variables clínicas añadidas a un modelo de imagen no lo convierten en multimodal
   (anotar en el detalle). `clinical_tabular` = variables clínicas, patológicas,
   de laboratorio o de registro/EHR estructurado.
10. `ai_family`: `statistical_model` = regresión/Cox/nomograma sin regularización
    ni aprendiz de ensamble y sin encuadre de ML; LASSO, árboles, SVM, kNN,
    boosting = `classical_ml`; `hybrid` = extractor de rasgos profundo + clasificador
    no neuronal (o a la inversa); `deep_learning` = red de extremo a extremo.
11. `comparator`: ablaciones y modelos base internos = `ablation_or_baseline_model`.
12. `patient_outcomes_reported`: `no` | `prognostic_association` (supervivencia/
    recurrencia por clase predicha) | `decision_or_outcome_impact` (el uso del
    modelo cambió decisiones o desenlaces).
13. `clinical_translation_stage`: solo validación interna o temporal del mismo
    centro → `research_only`; con centro o dataset externo → `retrospective_validation`;
    diseño prospectivo → `prospective_validation`; evidencia de impacto
    (`decision_impact_*`) → `implementation_evaluated`. **Aclaración (2026-10-07, lote 1):** el estadio sigue a `validation_level`, no al diseño del estudio: una cohorte prospectiva con solo CV interna del modelo es `research_only`.
    Regla 11 (`comparator`): comparar otros algoritmos del mismo paper = `other_ai_model`; ablaciones del mismo modelo = `ablation_or_baseline_model`.
14. Disponibilidad: datos de entrada públicos con accesión = `yes`; "no datasets
    generated/analysed" = `no`; acceso restringido por solicitud = `upon_request`
    con nota. `reporting_guideline` solo si el paper dice haberla seguido
    (no si solo la cita).
15. Inconsistencias internas del paper: iniciar `extraction_note` con
    `INCONSISTENT:` y preferir los valores de tablas sobre el texto.
16. Posible fuga de datos, solapamiento de cohorte con otro registro u otros
    problemas de validez: describir **factualmente** en `extraction_note`; no
    re-juzgar el appraisal.
17. Información solo en suplementos no disponibles: campo `NR`,
    `supplement_needed=yes`; no se completa a mano.
18. Cada campo numérico clave lleva localización (`n_source`, `performance_source`).
19. Lectura: `pdftotext` sin `-layout` primero (revistas de dos columnas);
    `-layout` solo para tablas; leer métodos, resultados, tablas, discusión,
    limitaciones, funding, COI y disponibilidad; omitir referencias. Contrastar
    cifras del resumen con resultados/tablas.

## Mínimo para `complete`

`tumor_site`, `stage_context`, `study_design`, `data_source`, `n_analytic`,
`therapeutic_decision`, `data_modality`, `ai_task`, `ai_family`, `algorithm`,
`primary_outcome`, `performance_metric` + `performance_value`,
`validation_level`, `clinical_translation_stage`. (`NA` cuenta como completo;
`NR` en `stage_context` o `n_analytic` impide `complete`.)

## Column dictionary (orden del CSV)

| Column | Meaning / controlled vocabulary |
|---|---|
| record_id, study_label | ID canónico; `ApellidoPrimerAutorAño` desde el PDF |
| title, year, journal, doi | Heredados de la cohorte |
| publication_status | `peer_reviewed` \| `preprint` |
| therapeutic_axis | Heredado (`neoadj_pcr_ww`, `biomarker_actionable`, `tox_surg`, `metastatic_systemic`, `adjuvant_risk`, `llm_dss`, `other`); no re-clasificar |
| tumor_site | `colon` \| `rectum` \| `colorectal` \| `colorectal_plus_other` |
| tumor_site_detail | Texto libre |
| stage_context | `early_resectable` (I–II / adyuvante) \| `locally_advanced_resectable` (III/alto riesgo, incl. neoadyuvancia) \| `locally_advanced_rectal` (recto cT3-4/N+ en nCRT/TNT) \| `metastatic` \| `mixed_all_stages` \| `NR` \| `NA` |
| stage_detail | Estadios/cTNM reportados |
| study_design | `retrospective_cohort` \| `prospective_cohort` \| `trial_secondary_analysis` \| `case_control` \| `cross_sectional` \| `bioinformatic_public_data` \| `simulation_or_vignette` \| `implementation_before_after` \| `other` |
| setting | `single_center` \| `multicenter` \| `public_database` \| `mixed` \| `NR` \| `NA` |
| country | País(es) o `NR`/`NA` |
| data_source | `own_cohort` \| `public_database` \| `trial_data` \| `multiple` |
| data_source_detail | Cohortes/datasets |
| n_analytic, n_unit, n_source | Total del estudio; `patients` \| `samples` \| `images` \| `scenarios` \| `other`; localización |
| n_performance | N que sostiene la métrica titular |
| n_train, n_test | Enteros o `NR`/`NA` |
| therapeutic_decision | `neoadjuvant_response` (pCR/TRG/downstaging) \| `organ_preservation_watch_wait` \| `surgical_approach_or_complications` \| `adjuvant_chemo_selection` \| `systemic_therapy_selection_response` \| `immunotherapy_msi_selection` \| `targeted_therapy_biomarker` \| `radiotherapy_planning` \| `toxicity_prediction` \| `treatment_intensification` \| `treatment_recommendation_llm` \| `discovery_no_decision` \| `other` |
| therapeutic_decision_detail, treatment_context | Decisión que el paper dice informar (sus palabras); tratamiento en cuestión |
| data_modality, data_modality_detail | `radiology_imaging` \| `digital_pathology` \| `endoscopy` \| `clinical_tabular` \| `genomics` \| `transcriptomics` \| `multiomics` \| `text_ehr` \| `multimodal` \| `other`; detalle |
| ai_task | `classification` \| `regression` \| `survival_prediction` \| `segmentation_plus_prediction` \| `signature_discovery` \| `recommendation_generation` \| `other` |
| ai_family | `classical_ml` \| `deep_learning` \| `llm` \| `hybrid` \| `statistical_model` |
| algorithm, input_features | Texto |
| comparator, comparator_detail | `none` \| `clinical_model` \| `clinician_or_expert` \| `guideline_or_tumor_board` \| `other_ai_model` \| `ablation_or_baseline_model` \| `other`; texto |
| primary_outcome | Desenlace objetivo del modelo |
| performance_metric | `AUC` \| `C-index` \| `accuracy` \| `sensitivity_specificity` \| `concordance_agreement` \| `HR` \| `other` \| `NR` |
| performance_value | Texto con todas las cohortes/métricas reportadas |
| primary_metric_value, primary_metric_ci_low, primary_metric_ci_high | Números (escala nativa, p. ej. AUC 0.85) o `NR` |
| performance_cohort | `train` \| `cross_validation` \| `internal_test` \| `temporal` \| `external` \| `prospective` \| `NR` |
| performance_source | Localización del valor |
| patient_outcomes_reported | `no` \| `prognostic_association` \| `decision_or_outcome_impact` |
| patient_outcomes_detail | Texto breve |
| validation_level | `none` \| `internal_cv` \| `internal_holdout` \| `temporal_same_center` \| `external_retrospective` \| `external_multicenter` \| `prospective` \| `decision_impact_observational` \| `decision_impact_demonstrated` |
| validation_basis | `performance_metric` \| `outcome_association` \| `NA` |
| validation_detail | Diseño real de validación |
| calibration_reported, explainability_reported | `yes` \| `no` |
| clinical_translation_stage | `research_only` \| `retrospective_validation` \| `prospective_validation` \| `implementation_evaluated` |
| code_availability, data_availability | `yes` \| `no` \| `upon_request` \| `NR` |
| reporting_guideline | `TRIPOD` \| `TRIPOD-AI` \| `CLAIM` \| `STARD` \| `REMARK` \| `other` \| `none_stated` |
| funding, conflicts_of_interest | Texto o `NR`/`none_declared` |
| key_finding, limitations_as_stated, other_models_outcomes | Texto |
| supplement_needed | `yes` \| `no` |
| extractor_id, extraction_mode, extracted_at | `REV-ALCIDES`; `ai_assisted_single_reviewer`; `YYYY-MM-DD` |
| extraction_status | `complete` \| `partial` \| `needs_review` |
| extraction_note, needs_review, batch | Texto; `yes` \| `no`; `batch-01`… |

`needs_review` y `extraction_status` son independientes: un registro puede estar
`complete` con `needs_review=yes` (decisión de codificación discutible ya
resuelta con una elección y una nota).
