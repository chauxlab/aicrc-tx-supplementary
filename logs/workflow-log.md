# Workflow log — AICRC_TX

## 2026-09-09 — Preparación inicial y protocolo

- Scaffold creado. `review.yaml` y `protocol/protocol.md` redactados.
- Fase actual del ciclo: **protocolo** (redactado, pendiente de aprobación).
- Siguiente fase: **búsqueda** — Canal A (PubMed) por bloques + Canal B
  (Europe PMC API REST); dedup → intake RIS (`REC-AICRCTX-000001…`) →
  cribado T/A doble R1/R2.
- Método de referencia: `reviews/AIBIO_GU/` (misma sesión).

## 2026-09-09 — Búsqueda Canales A+B e intake

1. Protocolo aprobado. Canal A PubMed en 3 sub-bloques de decisión
   (neoadyuvancia 400 / respuesta 195 / selección 332) → 927 brutos →
   **787 únicos** por PMID (ecuación ajustada tras 1ª pasada de 1.080).
2. Metadatos del Canal A vía Europe PMC (EXT_ID por lotes de 120);
   786/787 cubiertos, 1 vía conector PubMed (`aicrc_fetch.py`).
3. Canal B: búsqueda Europe PMC `TITLE:`/`ABSTRACT:` (mismos 3 sub-bloques),
   636 brutos → **61 nuevos** tras dedup cruzado.
4. RIS combinado (`aicrc_build_ris.py`) →
   `searches/ris/AICRC_TX_pubmed_europepmc_2026-09-09.ris` (848 entradas).
5. **Intake** (`review_intake_ris.py`): 848 RIS → `duplicate_candidate_rows=4`
   → 2 pares de preprints duplicados resueltos vía
   `archive/intake_2026-09-09/derived/duplicate_resolution.csv` → **846
   canónicos** (`REC-AICRCTX-000001`…`000848`, huecos 000818 y 000844;
   839 con abstract; 59 del Canal B). Backup de la base en scratchpad.
   `import_batch_id=IMP-SR-AICRC-TX-2026-09-02-RIS` (fecha `TODAY` fija del
   script, no editada).
6. Snapshot:
   `data/process_snapshots/2026-09-09__initial_sqlite_intake_dedup.csv`.
7. **Búsqueda en bases cerrada.** Siguiente: cribado T/A doble R1/R2.

## 2026-09-13 — Cribado título/abstract R1 (Alcides) completado

- Cribados los 846 registros de `screening/exports/2026-09-09__cribado-R1-chaux.csv`
  aplicando los criterios de elegibilidad de `protocol/protocol.md` (regla de oro:
  ¿el modelo de IA busca ayudar a decidir tratamiento o predecir
  respuesta/tolerancia a un tratamiento en CCR?).
- Resultado: **422 Included, 355 Excluded, 69 Maybe** (846 total).
- Motivos de exclusión: `wrong outcome` (141, mayoría pronóstico puro sin vínculo
  con decisión terapéutica — supervivencia, recurrencia, metástasis a distancia
  sin nexo explícito), `wrong publication type` (77, revisiones/editoriales/
  cartas/consensos/protocolos sin datos primarios), `wrong intervention` (70,
  sin IA/ML o solo tamizaje/diagnóstico/estadificación sin nexo decisional),
  `wrong study design` (34, preclínico/PDX/organoides/bibliométrico/
  metodológico puro), `wrong population` (33, no colorrectal o multi-cáncer sin
  desglose de CCR).
- `Maybe` reservado sobre todo para revisiones/metaanálisis de tema central
  (p. ej. IA prediciendo pCR/MSI/respuesta) marcadas para contexto y
  referencias, no como incluidas, y para un puñado de estudios primarios
  ambiguos (vínculo con decisión terapéutica no explícito).
- Archivo resultante: `screening/imports/2026-09-09__cribado-R1-chaux-COMPLETO.csv`
  (846/846 con `decision` no vacío; `exclusion_reason` controlado y presente en
  todos los `Excluded`; verificado sin registros faltantes).
- **Siguiente paso:** enviar/recibir el cribado R2 (Paola Britos) y reimportar
  ambos con `scripts/import_ta_screening_decisions.py` para calcular kappa y
  resolver conflictos por consenso.

## 2026-09-16 — Recuperación de texto completo cerrada + paquete cribado FT R2

- Ingesta de `paperpile-files.zip` (usuario) con
  `scripts/ingest_paperpile_full_texts.py --review reviews/AICRC_TX`:
  364 referencias en el catálogo Paperpile, **266 con PDF adjunto** matched a
  registros `pending_retrieval` (370 → 104 pendientes).
- Recuperación automática Unpaywall/Europe PMC con
  `scripts/retrieve_aicrc_tx_full_texts.py` (nuevo, adaptado de
  `retrieve_aibio_gu_full_texts.py`): **+5** (incluye 4 preprints Research
  Square/bioRxiv) → 99 pendientes.
- Chequeo manual vía BrowserOS neo sobre 32/99 DOIs pendientes (Springer,
  ScienceDirect, Wiley, LWW): **0/32 recuperados** — todos paywall duro, sin
  acceso institucional activo en el navegador del usuario, consistente con lo
  ya detectado por Unpaywall. Decisión del usuario: cortar el chequeo manual
  ahí (no exhaustivo sobre los 99) y declarar el resto `unavailable`.
- **Cierre:** de 370 Included → **271 retrieved (73.2%)** / **99 unavailable**
  (paywall sin suscripción institucional, sin OA legal, documentado en
  `full_texts.note` por registro).
- Paquete cribado FT R2 (Paola) armado:
  `screening/full_text/paquete-R2-britos-2026-09-16/` (CSV ciego 271
  registros + LEEME con criterios de elegibilidad del protocolo). PDFs se
  acceden directamente en `screening/full_text/REC-AICRCTX-*.pdf` (no
  empaquetados en zip).
- **Siguiente paso:** enviar paquete a Paola; en paralelo, R1 (Alcides)
  criba la misma planilla (`cribado-FT-R2-britos.csv`, 271 registros) en
  ciego. Al volver ambas: `screening/imports/` → conflictos + consenso FT →
  appraisal (MMAT + PROBAST/TRIPOD-AI) → extracción.

## 2026-09-21 — Handoff FT R2 listo para enviar

- Planilla ciega verificada: 271 filas, set = `full_texts.retrieved`,
  columnas `decision` / `exclusion_reason` / `note` vacías, 271 PDF
  `REC-AICRCTX-*.pdf` presentes en `screening/full_text/`.
- `LEEME-instrucciones-FT-R2.md` actualizado con la regla de **vínculo
  analítico** del consenso T/A (2026-09-16) y los clusters operativos
  (firmas in silico, MSI/KRAS por imagen, estadificación vs decisión,
  complicaciones quirúrgicas, revisiones). Fecha de envío 2026-09-21.
- Copia de envío: `screening/exports/2026-09-21__HANDOFF-cribado-FT-R2-Britos.md`
  + zip `screening/exports/paquete-R2-britos-2026-09-21.zip` (LEEME +
  CSV; sin PDFs).
- **Enviado a Paola** (2026-09-21): zip + enlace Drive a
  `screening/full_text/` (271 PDF).
- **Siguiente paso:** R1 (Alcides) criba la misma planilla en ciego.
  Al volver ambas: `screening/imports/` → conflictos + consenso FT →
  appraisal (MMAT + PROBAST/TRIPOD-AI) → extracción.

## 2026-09-28 — Cribado FT R1 cerrado

- Planilla R1 completa sobre los 271 PDF recuperados:
  `screening/imports/2026-09-28__cribado-FT-R1-chaux-COMPLETO.csv`.
- **229 Included / 42 Excluded** (`wrong outcome` 20, `wrong publication
  type` 17, `wrong intervention` 5). Sin `Maybe`. Toda exclusión tiene
  motivo controlado y nota.
- Criterio: vínculo analítico del consenso T/A, calibrado en un piloto de
  16 (`screening/full_text/r1/criterio-ft-r1.md`).
- No importado a SQLite. R2 sigue pendiente de devolución.
- **Siguiente paso:** al volver la planilla de Paola, conflictos + consenso
  FT → appraisal (MMAT + PROBAST/TRIPOD-AI) → extracción.

## 2026-09-28 — Pausa: R2 recibido, motivos incompletos

- Paola devolvió `2026-09-27__cribado-FT-R2-britos-COMPLETO.csv`
  (Descargas; no copiado al expediente). 271 filas, set = R1, columnas
  de identidad intactas. Decisiones: 232 Included / 39 Excluded.
- **No importable:** 25 exclusiones tienen `exclusion_reason` vacío; el
  motivo está solo en `note`. Las otras 14 usan `wrong intervention`.
- Acuerdo R1×R2: 224/271 (82,7 %), kappa 0,32, 47 conflictos. No resolver
  consenso hasta que Paola complete los códigos.
- **Pausa.** Siguiente: planilla R2 corregida → `screening/imports/` →
  conflictos + consenso FT.

## 2026-09-28 — Consenso FT R1×R2 cerrado

- R2 corregida depositada:
  `screening/imports/2026-09-28__cribado-FT-R2-britos-COMPLETO.csv`
  (232 Included / 39 Excluded; códigos completos).
- Acuerdo 224/271 (82,7 %), kappa 0,32, **47 conflictos** resueltos en
  `screening/conflicts/2026-09-28__conflictos-FT-R1-R2.csv`.
- Consenso final: **227 Included / 44 Excluded**
  (`screening/conflicts/2026-09-28__consenso-FT-final-271.csv`).
  Motivos: `wrong outcome` 20, `wrong publication type` 17,
  `wrong intervention` 7.
- Política en conflictos (regla de vínculo analítico + clusters T/A):
  complicaciones quirúrgicas y toxicidad de quimioterapia → Included;
  MSI/dMMR/KRAS predichos por IA → Included (aunque el paper diga
  «triage»); preprints/pósters duplicados → Excluded; pronóstico sin
  beneficio diferencial → Excluded; sin clasificador de IA/ML → Excluded.
- Import SQLite: `IMP-SR-AICRC-TX-2026-09-28-FT-R1` +
  `IMP-SR-AICRC-TX-2026-09-28-FT-CONSENSUS`. Los 99 `unavailable`
  siguen Pending en `full_texts` (consenso T/A Included sin texto).
- **Siguiente paso:** appraisal (MMAT v.2018 + PROBAST/TRIPOD-AI) sobre
  los 227 Included → extracción. Canal C post-appraisal/extracción.

## 2026-09-28 — Appraisal andamiaje + piloto listo

- Codebook y planillas en `risk_of_bias/` (MMAT + PROBAST/TRIPOD-AI).
- Proceso: R1 escala completa; R2 verifica muestra ~10–15 %.
- Piloto estratificado n=20 cerrado (`AICRC_TX_appraisal_PILOT_20.csv` +
  `AICRC_TX_appraisal_PILOT_SUMMARY.md`). Codebook congelado.
  Distribución: MMAT low 12 / moderate 6 / high 2; PROBAST high 14 /
  unclear 4 / low 2; TRIPOD partial 13 / adequate 7. 0 correcciones
  mecánicas post-fusión.
- **Siguiente:** escala R1 sobre los 207 restantes (lotes paralelos).

## 2026-09-28 — Escala R1 appraisal cerrada

- 207 appraisados en 7 lotes; fusión → `AICRC_TX_appraisal_FULL_227.csv`.
- 0 correcciones mecánicas; 0 cannot_appraise.
- Muestra R2 n=31 lista (`AICRC_TX_appraisal_R2_VERIFY_31.csv`).
- **Siguiente:** verificación R2 → acuerdo → import SQLite → extracción.

## 2026-09-28 — Paquete R2 appraisal listo para envío

- Carpeta: `risk_of_bias/paquete-R2-britos-2026-09-28/`
- Zip: `screening/exports/paquete-appraisal-R2-britos-2026-09-28.zip`
  (LEEME + planilla 31 + codebook; sin PDFs; sin clave R1).
- Mensaje: `screening/exports/2026-09-28__mensaje-appraisal-R2-Britos.txt`
- **Siguiente:** enviar a Paola → recibir planilla → acuerdo.

## 2026-09-29 — R2 appraisal recibida; contraste hecho

- Planilla: `risk_of_bias/appraisal-R2-britos-verify-31-DONE.csv` (31/31).
- Resumen: `risk_of_bias/AICRC_TX_appraisal_R2_VERIFY_SUMMARY.md`
  (acuerdo MMAT 25/31 κ0.40; PROBAST 27/31 κ0.49; TRIPOD 24/31 κ0.51;
  aplicabilidad 19/31 κ−0.10).
- Borrador de respuesta / propuestas de consenso:
  `screening/exports/2026-09-29__mensaje-appraisal-R2-consenso-Britos.txt`.
- **Siguiente:** confirmar consenso con Paola → parchear FULL_227 →
  import SQLite → extracción.

## 2026-09-29 — Pausa: preconsenso enviado a Paola

- Paola aceptó adoptar 1–4 (000318 moderate, 712/752/447 app high,
  823 TRIPOD inadequate) y frenó el cierre por tres puntos.
- Análisis: `risk_of_bias/AICRC_TX_appraisal_R2_PRECONSENSUS_ANALYSIS.md`.
  Propuesta enviada por Gmail (2026-09-29): re-pasar solo aplicabilidad
  en los 227; retirar “más conservador gana”; releer c4 de 000395;
  000476 no se baja por contagio. No parchear solo la muestra.
- **Pausa** hasta la respuesta. No cerrar CSV de consenso ni FULL_227.

## 2026-09-30 — Reanudación: reglas de aplicabilidad v2 en borrador

- Respuesta de Paola leída; redactado el borrador de reglas H1–H7/U1–U3 y
  el borrador de respuesta (Gmail). Aún no se cierra CSV de consenso ni se
  parchea FULL_227.

## 2026-10-01 — Congelación de reglas v2 y paquete de muestra nueva

- Reglas v2 congeladas (FROZEN); muestra nueva n=25 sorteada (semilla
  20261001); paquete a Paola enviado por Gmail. Pausa hasta su respuesta.

## 2026-10-05 — Paquete de aplicabilidad subido al Drive y respuesta a Paola

- Paola (2026-10-02) envió la planilla v2 de los 31 (`risk_of_bias/appraisal-R2-britos-verify-31-DONE-v2.csv`; 000395 moderate, 000510 y 000823 low concern) y avisó que no recibió el paquete de aplicabilidad.
- Causa: el paquete solo estaba en el expediente local. Copiado a `revisor-assets/AICRC_TX/screening/paquete-R2-britos-aplicabilidad-2026-10-01/` del Drive compartido (25 PDFs ya presentes en `screening/full_text/`). Enlace y confirmación de `rule_cited` enviados por Gmail.
- Pendiente: planilla de los 25 de Paola → R1 puntúa sin ver R2 → kappa + acuerdo crudo → aplicar v2 de los 31 → re-pase 227 → consenso → FULL_227 → SQLite.

## 2026-10-05 — Planilla de aplicabilidad de Paola (25) y R1 ciego por subagente

- Paola entregó `aplicabilidad-R2-britos-muestra-25-DONE.csv` (Drive, carpeta del paquete): 11 low, 9 high, 5 unclear. Copia local no archivada; el original queda en el Drive.
- R1 puntuado por un subagente que solo vio reglas v2 congeladas, planilla en blanco y PDFs (sin R1KEY, sin Gmail, sin planilla de Paola): `risk_of_bias/AICRC_TX_applicability_NEWSAMPLE_25_R1_BLIND.csv` (12 low, 9 high, 4 unclear). Comparación recién después de cerrar R1: `..._NEWSAMPLE_25_COMPARE.csv`.
- Acuerdo por categoría 21/25 = 84 %; kappa 0,745; AC1 0,767; PABAK 0,76.
- Discordancias de categoría (a resolver releyendo, sin desempate automático): 000107, 000308, 000385, 000511. Discordancias solo de regla: 000204 (H1/H4), 000222 (U1/U2), 000485 (H1;H3/H4), 000809 (parcial).
- Declaraciones de independencia: Paola no es ciega en 000791, 000809 (excluidos por ella en FT) ni 000471 (conocía el MMAT de R1). Quien orquestó el flujo (Claude) leyó el resumen del correo de Paola (distribución, reglas por conteo, casos fronterizos) antes de lanzar al subagente; el subagente no recibió nada de eso.
- Vacíos de reglas señalados por ambos revisores de forma independiente: triage diagnóstico de MSI (000460, 000791, 000809; enrutado por H1), comparación metodológica (000511). Candidato a v3.
- Pendiente: resolver discordancias con Paola → decidir v3 → aplicar v2 de los 31 → re-pase 227 → consenso → FULL_227 → SQLite.
- Correo a Paola enviado (2026-10-05): resultado de la comparación, 4 discordancias de categoría, 4 de regla, vacíos y propuesta de v3 mínima (triage MSI, comparación metodológica, umbral de U3). Pausa hasta su respuesta.
- Decisión del 2026-10-05: **pausa hasta recibir el correo de Paola**. Pendiente abierto: redacción de la declaración de R1/R2 para el reporte final. El registro de procedencia (R1 de la muestra de 25 puntuado por un subagente de Claude, ciego) se mantiene tal cual en este log y en el correo enviado; la fórmula de la declaración final se fija con el usuario, sin alterar este registro.

## 2026-10-07 — Respuesta de Paola (2026-10-06): cierre sin v3 y re-pase de los 171

- Paola cede las cuatro discordancias de categoría (000107, 000308, 000385, 000511): se consolidan como en la columna R1 (`..._NEWSAMPLE_25_R1_BLIND.csv`). No se abre v3; las reglas v2 siguen congeladas.
- Discordancias solo de regla, como se resolvieron: 000222 → U1; 000485 → H1;H3; 000809 → H1;H4; 000204 → H1 principal, H4 secundaria.
- Los dos vacíos (triage diagnóstico de MSI; comparación metodológica) quedan como **nota al pie del reporte**, no como regla nueva.
- Instrucción de Paola: aplicar las tres correcciones de los 31 (000395 moderate; 000510 y 000823 fuera de aplicabilidad), re-pasar los 227 y consolidar el consenso.
- Re-pase: los 25 (R1 ciego) y los 31 (planilla v2) ya tienen puntaje v2; se re-pasan los 171 restantes en 9 lotes de 19 por subagentes de Claude que solo ven reglas v2 + PDF (sin v1, sin Gmail, sin puntajes de Paola). Entradas/salidas en `risk_of_bias/applicability_repass/`.

## 2026-10-07 — Re-pase de aplicabilidad v2 de los 171 terminado; tabla v2 de los 227

- 9 lotes de 19 (subagentes de Claude, solo reglas v2 + PDF): 121 low / 38 high / 12 unclear. Validación mecánica: 171 ids únicos, regla citada coherente con el veredicto. (El resumen verbal del lote 8 decía 8 high; el CSV tiene 7 y es el que vale.)
- Tabla consolidada `risk_of_bias/AICRC_TX_applicability_v2_ALL_227.csv` (v1, v2, regla, fuente): 157 low / 53 high / 17 unclear; 74 cambian respecto a v1. Fuentes: 171 re-pase, 31 planilla v2 (sin regla citada en la tabla), 25 columna R1 ciega (con las cuatro discordancias consolidadas como R1, según Paola).
- Cobertura parcial declarada por los subagentes (texto con pdftotext en vez de lectura de página): lote 5 (000025, 000046, 000109, 000127, 000156, 000180, 000528, 000651, 000763), lote 2 (000010–000176 y primeras páginas de 000193), lote 6 (solo primeras 4–6 páginas por PDF). Pendiente decidir segunda lectura.
- Todavía NO se parchea FULL_227 ni SQLite.

## 2026-10-07 — Segunda lectura, consenso de los 31, FULL_227 v2, SQLite y handoff

Decisiones tomadas por el asistente (delegadas por el usuario; a confirmar con Paola donde se indica):

1. **Segunda lectura con texto completo** de 55 registros: los de cobertura parcial (lotes 2, 5, 6) más los 16 con H4 y los borderline. Prevalece sobre la 1.ª pasada; cambian 15 (6 low→high, 1 unclear→high, 1 high→low).
2. **Criterio único para H4** (lectura literal de H4, solo en los 171): autodeclaración del propio estudio como PoC/piloto/exploratorio/generador de hipótesis/no desplegable, en cualquier sección, o título "Feasibility/Preliminary Study"; no cuenta validación futura genérica ni títulos de referencias. Barrido por palabras clave en los 227 PDF (contexto revisado a mano): 000041, 000449, 000019, 000254, 000418, 000463, 000613 → high H4; 000304 y 000458 reciben H4 secundaria; 000038 queda low (la frase es sobre un subanálisis). **Pendiente de confirmación de Paola** (no es v3: es lectura de H4).
3. **Los 25 y los 31 no se tocan** (muestra ciega y planilla de R2 fijadas). Sensibilidad: 000031, 000147, 000371 serían high H4 bajo el criterio único.
4. 000742: lecturas discrepantes (high H4 vs low); se deja la del texto completo (low).
5. 000090: se mantiene (inclusión zanjada por consenso, Regla A de R2); solo se juzga aplicabilidad (H3).
6. **Consenso de los 31**: relectura de 111 ítems discordantes sobre la evidencia (R1 43 / R2 62 / ninguno 6), puntos 1–4 de la propuesta y 000395 c4 aplicados, overalls recalculados con la regla mecánica (85 cambios de campo en 26 registros; detalle en `..._R2_VERIFY_31_CONSENSUS_changes.csv`). Resultado acorde con lo pactado: 000318 moderate/unclear/adequate; 000395 moderate; 000823 TRIPOD inadequate y aplicabilidad low (H5).
7. **FULL_227 parcheado**: aplicabilidad v2 en los 227 + columnas `applicability_v1`, `applicability_v2_rule`, `applicability_v2_source`, `applicability_v2_rationale`, `consensus_31`. Verificación mecánica de overalls: 0 violaciones. Distribución: MMAT 8/52/167; RoB high/unclear/low 187/37/3; aplicabilidad 66 high / 16 unclear / 145 low; TRIPOD 90/125/12.
8. **SQLite** (`reviews/AICRC_TX/data/reviews.sqlite`, ignorada por git): `scripts/import_aicrc_tx_appraisal.py` (nuevo, adaptado de AIBIO_GU): 227 studies, 4540 filas `risk_of_bias`.
9. **Handoff**: `data/prisma/AICRC_TX_prisma_counts_2026-10-07.csv`, copia `risk_of_bias/AICRC_TX_rob_assessments_2026-10-07.csv`, nota `manuscript/appraisal-handoff-note.md`; export regenerado → `ready_for_sesion2: true`.

Abierto: extracción no iniciada; protocolo sin registro; redacción de la declaración de procedencia R1/R2 (la fija el usuario); respuesta a Paola sobre el criterio de H4 y la sensibilidad.

## 2026-10-07 — Extracción: diseño, piloto y congelación del codebook v1.0

Contexto: revisión integrativa con fecha de envío vencida; el usuario pidió extracción por **un único revisor** optimizando pasos para iniciar la redacción sin perder rigor.

Decisiones:
1. **Un extractor, asistido por IA** (subagentes de Claude bajo `REV-ALCIDES`, `extraction_mode=ai_assisted_single_reviewer`), sobre PDF completo. Sin doble extracción de los 227 (el protocolo no la exige).
2. **Compensación del rigor**: piloto estratificado (12 registros, semilla 20261007: neoadj 3, biomarcador 2, tox/cirugía 2, metastásico 2, LLM/DSS 1, otros 1, adyuvante 1); codebook congelado; localización de fuente en N y desempeño; validación mecánica (`reviews/scripts/validate_aicrc_tx_extraction.py`); **verificación ciega independiente del 10 %** con acuerdo por campo; `needs_review` resuelto o declarado.
3. **Piloto → 17 cambios al codebook** (v1.0 FROZEN, `extraction/AICRC_TX_extraction_codebook.md`), aceptados de los dos informes de piloto: modelo principal con desempate; `n_analytic` total + `n_performance`; `n_test` en lugar de `n_test_external`; `stage_context` + `locally_advanced_resectable`/`NA`; `study_design` + `implementation_before_after`; `validation_level` + `temporal_same_center` y separación `decision_impact_observational/demonstrated`; `validation_basis`; `external_multicenter` exige ≥2 sitios; regla explícita de `clinical_translation_stage`; `neoadjuvant_response` (pCR/TRG/downstaging), `treatment_intensification`, `discovery_no_decision` y desempate por tratamiento predicho; `clinical_tabular`; definiciones `statistical_model`/`classical_ml`/`hybrid`; `ablation_or_baseline_model`; `patient_outcomes_reported` en tres niveles; métrica titular numérica con IC; `publication_status`; `supplement_needed`; reglas de disponibilidad, guía de reporte, `INCONSISTENT:`; lectura sin `-layout` primero.
4. **Rechazado** (para no inflar el formulario): columna de fuga de datos y de solapamiento de cohorte (se describen factualmente en `extraction_note`, sin re-juzgar el appraisal), `n_events`.
5. Los 12 del piloto se **re-extraen con v1.0** dentro de la escala (el piloto v0 queda archivado en `extraction/pilot_v0/`).
6. Escala: 29 lotes de 8 (`extraction/batches/`), oleadas de 8 subagentes en paralelo.

## 2026-10-07 — Extracción: avance de la escala (153/227) e incidencias

- Lotes 1–18 completos (144 registros) + lotes 19 (4/8) y 20 (5/8) parciales; validador mecánico: 0 problemas salvo 000043 (n_performance en fotogramas > n_analytic en pacientes; diferencia de unidad real, justificada en la nota).
- **Incidencias de ejecución**: dos cortes por límite de sesión del servicio (lotes 1–8 en la primera oleada, sin pérdida de datos útiles pero repetidos; lotes 17, 19 y 20 en la segunda). Mitigación: oleadas de 4, fila escrita al terminar cada registro, carpeta temporal propia por lote (un lote sobrescribió un auxiliar compartido; sin efecto en las salidas).
- Aclaraciones al codebook v1.0 durante la escala (no cambian el esquema): el estadio de traducción sigue a `validation_level`, no al diseño (cohorte prospectiva con CV interna = `research_only`); `comparator`: otros algoritmos = `other_ai_model`, ablaciones = `ablation_or_baseline_model`. Corrección del validador (vocabulario `treatment_intensification`/`discovery_no_decision`; regla de ≥2 sitios externos pasa a verificación manual).
- Fila 000094 recodificada de `other` a `discovery_no_decision` tras corregir el vocabulario del validador.
- Reanudación: lotes 19r, 20r, 21, 22 lanzados; restan 23–29.

## 2026-10-07 — Extracción: escala completa (227/227) e importación a SQLite

- 29 lotes (8 registros c/u; el 29 con 3) extraídos con codebook v1.0; consolidado en `extraction/AICRC_TX_extraction_FULL_227.csv` (copia `..._wide.csv` para el handoff). Cobertura 227/227, sin duplicados ni faltantes. Validador mecánico: 0 problemas salvo 000043 (diferencia de unidad fotogramas/pacientes, documentada).
- Estado: 211 complete / 16 partial (estadio, N o métrica no reportados); 86 con `needs_review=yes` (mayoría: modelos co-primarios sin ganador declarado, resuelto por regla 2); 67 con nota `INCONSISTENT:` (cifras que difieren entre resumen, texto y tablas; se usó la tabla); 46 con `supplement_needed=yes`.
- Distribución principal: decisión terapéutica `neoadjuvant_response` 136, `immunotherapy_msi_selection` 20, `systemic_therapy_selection_response` 17, `organ_preservation_watch_wait` 15; modalidad radiología 134, clínica tabular 24, multimodal 21, patología digital 17, transcriptómica 17; validación interna (holdout/CV) 120, externa retrospectiva 51, multicéntrica 24, temporal 13, prospectiva 8, impacto observacional 1; etapa de traducción `research_only` 143, `retrospective_validation` 75, `prospective_validation` 8, `implementation_evaluated` 1.
- SQLite: `scripts/import_aicrc_tx_extraction.py` (nuevo, adaptado de AIBIO_GU): 227 registros × 65 variables = 14 755 filas en `extractions` (con `value_number`/`value_integer` tipados para N y métrica titular).
- Pendiente: verificación ciega del 10 % (24 registros, semilla 20261008, 3 tandas de 8) y su acuerdo por campo; tabla maestra de síntesis; regenerar handoff.

## 2026-10-07 — Verificación ciega del 10 %, adjudicación, síntesis y handoff completo

- **Muestra**: 24 registros (10,6 %), aleatoria estratificada por eje (neoadj 12, biomarcador 3, tox/cirugía 3, otros 2, metastásico 2, adyuvante 1, LLM/DSS 1), semilla 20261008. Re-extracción por 3 subagentes que **no vieron** la extracción principal (formulario reducido de 18 campos clave).
- **Acuerdo**: 366/384 (95,3 %). Por campo: 100 % tipo de tumor, diseño, modalidad, familia de IA, validación y etapa de traducción; 95,8 % decisión terapéutica, tarea, métrica, datos, guía; 91,7 % cohorte del desempeño y métrica titular; 87,5 % estadio, código y N analítico.
- **Adjudicación** de las 18 discrepancias contra el PDF (`extraction/qa/AICRC_TX_qa_adjudication.csv`): 15 cambios de campo en 7 registros — 000033 (n_analytic 25; código NR), 000109 (código y datos `no`), 000147 (métrica titular accuracy 80 en el test interno, regla 4), 000160 (tarea `other`), 000642 (N analizado 3581), 000799 (estadio NR → parcial), 000414 (código `upon_request`). Se mantuvo la extracción principal en 000043, 000056, 000357 y en estadio de 000033/000414 y unidad de 000192 (decisiones razonadas y documentadas). Cada fila cambiada lleva la trazabilidad en `extraction_note`.
- Normalización de `country` (USA/US → United States, UK, Korea): 10 filas.
- Tablas de síntesis: `analysis/AICRC_TX_map_{decision_x_modality,decision_x_translation_stage,decision_x_validation,modality_x_validation}.csv`; nota `manuscript/extraction-handoff-note.md` (corregidos dos errores propios detectados al verificar la nota contra los datos: 94,7 % de estudios 2020–2026 y una frase sin base).
- SQLite reimportada (227 × 65 = 14 755 filas); handoff regenerado con `extraccion.csv`, `rob.csv` y `prisma-counts.csv`; `ready_for_sesion2: true`.
- Abierto: criterio de H4 (confirmación de Paola), declaración de procedencia R1/R2 y de la extracción (fórmula la fija el usuario), correo a Paola sin enviar, protocolo sin registro, 86 `needs_review` retenidos con elección reglada, 46 estudios con información en suplementos no disponible.

## 2026-10-07 — Cierre de la fase REVISOR y traspaso al redactor

Decisiones del usuario: (1) **no se registrará el protocolo**; `protocol_ref: no registrado` en `review.yaml` y nota en `protocol/protocol.md`; (2) la declaración de procedencia se redacta describiendo lo ocurrido y se ajusta luego con el redactor; (3) criterios confirmados por el usuario, se cierra la fase y la sesión.
- Declaración de procedencia (borrador descriptivo + párrafo en inglés): `manuscript/provenance-declaration.md`. Puntos marcados **[CONFIRMAR]** porque el expediente no registra quién ejecutó el R1 del cribado T/A y de texto completo ni la puntuación R1 v1 del appraisal (¿manual o con IA?).
- Constancia: la confirmación del criterio de lectura de H4 consta como confirmación del usuario; no consta confirmación escrita de la segunda revisora. El correo a Paola no se envió.
- Handoff regenerado (`handoff-sesion2/`): `ready_for_sesion2: true`, `blocking_gaps: []`, con `extraccion.csv`, `rob.csv`, `prisma-counts.csv`, `incluidos.csv`, `excluidos-ft.csv`, `protocolo.md`. Notas para el redactor en `manuscript/` (`appraisal-handoff-note.md`, `extraction-handoff-note.md`, `provenance-declaration.md`).
- **Fase REVISOR cerrada.** Siguiente: REDACTOR Sesión 2 («Lee `REVISOR/reviews/AICRC_TX/handoff-sesion2/PROMPT_SESION2.md` y continúa la Sesión 2»).

## 2026-10-07 — Informe de eficiencia y plan de canal por API

- Consumo del appraisal y la extracción: ≈9,0 M de tokens reportados por subagentes (más corridas cortadas por el límite de sesión); la ventana de 5 h del plan Pro se agotó en 15–20 min.
- Decisión del usuario: configurar una cuenta de API (consola) y una clave; en la próxima sesión de REVISOR se prueba el canal por API. La clave irá solo como variable de entorno; `.env` agregado a `.gitignore`.
- Informe con cuellos de botella, procedimiento v2, plan de prueba de 12 artículos y lista de arranque: `manual/informe-eficiencia-y-canal-api-AICRC_TX.md`.
- Se pasa a REDACTOR (Sesión 2).
