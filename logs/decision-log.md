# Decision log — AICRC_TX

## 2026-09-09 — Creación del expediente y protocolo

- Se creó el expediente `AICRC_TX` con `scripts/create_review_scaffold.sh`.
- Tema: *Artificial Intelligence in Therapeutic Decision-Making in Colorectal
  Cancer: Current Evidence and Perspectives for Precision Oncology*. Revisión
  **integrativa** (Whittemore & Knafl), manuscrito en **inglés**, sin
  registro externo. Sin manuscrito ni preprint previo.
- `review.yaml`: `review_id: SR-AICRC-TX`, `review_type: integrative_review`,
  `r1_reviewer_id: REV-ALCIDES`, `r2_reviewer_id: REV-PAOLA`.
- Directorio de intake renombrado a `intake_2026-09-09`; placeholder
  `raw/rayyan` del scaffold eliminado (no se usa Rayyan).
- **Alcance definido con el usuario (2026-09-09):**
  - Población: CCR (colon y/o recto), cualquier estadio, incl. metastásico.
  - Concepto: **amplio** — toda decisión terapéutica (terapia sistémica,
    predicción de respuesta/toxicidad, neoadyuvancia y RPc, watch-and-wait
    en recto, cirugía, radioterapia, dosis/secuenciación). **Excluye**
    tamizaje, detección de pólipos, diagnóstico y estadificación pura, y
    pronóstico puro sin vínculo con decisión terapéutica.
  - Modalidades: todas (radiómica/imagen, patología digital, -ómicas, ML
    clínico/EHR, LLMs / sistemas de apoyo a la decisión).
  - Diseños: estudios primarios con cohorte humana; revisiones solo para
    contexto.
- Marco: **PICO** con desenlaces declarados.
- Appraisal previsto: **MMAT v.2018** + dominios **PROBAST/TRIPOD-AI**.
- Flujo: REVISOR-first (sin pasar por REDACTOR); Canal A PubMed + Canal B
  Europe PMC (API REST), igual que `AIBIO_GU`.
- `protocol/protocol.md` redactado. **Aprobado por el usuario** ("aprobado, avanzar").

## 2026-09-09 — Decisiones de búsqueda

- **Ajuste de ecuación (a pedido del usuario):** la ecuación amplia (3
  bloques con `treatment response`, `neoadjuvant`, `immunotherapy`,
  `precision oncology`, `treatment planning`, etc. sueltos) daba **1.080
  únicos**. Se restringió exigiendo señal de decisión más fuerte —frases
  específicas de respuesta patológica completa / watch-and-wait / regresión
  tumoral; predicción de respuesta o toxicidad; selección/estratificación/
  decisión terapéutica— → **787 únicos** (−27 %).
- Búsqueda dividida en 3 sub-bloques de decisión por el límite de 20
  operadores del conector PubMed; unión por PMID.
- **Canal B** ejecutado contra la API REST pública de Europe PMC (mismo
  patrón que AIBIO_GU); 59 registros nuevos. Metadatos del Canal A también
  vía Europe PMC (EXT_ID por lotes) para evitar 40 llamadas al conector
  PubMed; 1 PMID ausente en Europe PMC recuperado vía conector PubMed.
- **Deduplicación:** 2 pares de preprints duplicados resueltos
  (`archive/intake_2026-09-09/derived/duplicate_resolution.csv`): SORBET
  (mismo DOI, Canal A vs Canal B) y "Harness Behavioural Analysis…"
  (cross-posted en dos servidores).
- **Sin coautoría del usuario** en el corpus (verificado): no aplica nota de
  COI.
- Bases sin acceso (Embase/Scopus/WoS) omitidas; Canal C = rastreo de citas
  post-FT. Igual criterio que AIBIO_GU.

## 2026-09-16 — Cribado T/A: consenso R1×R2 cerrado (846)

- **R1 (Alcides)** completado y commiteado 2026-09-09 (`abb0075`): 422
  Included / 355 Excluded / 69 Maybe.
- **R2 (Paola)** recibido dos veces: una primera planilla quedó depositada
  sin procesar por inconvenientes de cribado; el 2026-09-15 Paola reenvió la
  versión corregida (`2026-09-09__cribado-R2-britos-COMPLETO.csv`, 38
  registros difieren de la versión previa). Se usó la versión corregida.
  336 Included / 211 Excluded / 299 Maybe. Kappa R1×R2 = **0.48**
  (moderado), con **292 conflictos** — la mayoría (237) por uso mucho más
  frecuente de "Maybe" por parte de R2 (299 vs 69 de R1), reflejo de estilo
  de cribado más que de discrepancia sustantiva.
- **Regla de política adoptada para consenso (decisión del usuario,
  2026-09-16):** para estudios de pronóstico puro donde el protocolo exige
  "vínculo con decisión terapéutica" pero el texto solo lo menciona de forma
  declarativa (frase-gancho en introducción/discusión sin análisis que lo
  respalde), se exige **vínculo analítico** (el estudio efectivamente
  compara/estratifica por tratamiento recibido, o cuantifica beneficio
  diferencial) para incluir. Frase-gancho sola → `Excluded/wrong outcome`.
  Regla propuesta originalmente por R2 (documentada en sus notas de cribado
  como "regla de 3 niveles"); el usuario eligió esta opción sobre la lectura
  literal más laxa del protocolo.
- **Clusters identificados y resueltos con regla explícita durante el
  consenso:**
  - Revisiones (sistemáticas/narrativas/scoping), incl. las que R2 marcó
    "Maybe" por ser temáticamente centrales → `Excluded/wrong publication
    type` (se conservan para contexto/rastreo de citas, no como estudio
    incluido; regla ya aplicada en AIBIO_GU).
  - Firmas transcriptómicas pronósticas con predicción de respuesta a
    inmunoterapia/quimioterapia derivada **in silico** (TIDE, pRRophetic,
    GDSC, CTRP) sin cohorte tratada con desenlace real (~57 registros) →
    `Excluded/wrong outcome`. Excepciones cuando había cohorte realmente
    tratada con desenlace real (p. ej. respondedores/no respondedores reales
    a bloqueo PD-1) → `Included`.
  - Biomarcador molecular accionable predicho por IA (MSI/MMR/dMMR/KRAS y
    mutaciones accionables) desde imagen, radiómica o histología (~35
    registros) → `Included`: aunque el propósito declarado suele ser
    "sustituir el test molecular", el biomarcador determina directamente
    elegibilidad para inmunoterapia o terapia anti-EGFR — vínculo analítico
    explícito con la decisión terapéutica, a diferencia de la
    caracterización/estadificación pura.
  - Caracterización o estadificación patológica preoperatoria (invasión
    linfovascular, ganglios, EMVI, depósitos tumorales) sin decisión
    terapéutica analizada → `Excluded/wrong intervention`.
  - Complicaciones tras cirugía oncológica (fuga anastomótica, infección de
    sitio quirúrgico, índice de complicaciones) → `Included` (toxicidad del
    tratamiento quirúrgico, categoría explícita del protocolo).
  - Ausencia total de IA/ML, o IA usada solo como herramienta de consulta/
    integración de datos (no como modelo predictivo) → `Excluded/wrong
    intervention`.
  - Cohortes mixtas (CRC + otro tumor) sin resultados de CRC separables en
    el resumen → `Excluded/wrong population`.
- **Consenso final:** 846 registros → **370 Included / 476 Excluded**.
  Importado con `import_ta_screening_decisions.py`
  (`IMP-SR-AICRC-TX-2026-09-16-TA-SCREEN`). Auditoría completa de los 292
  conflictos con rationale por registro en
  `screening/conflicts/2026-09-16__conflictos-TA-R1-vs-R2.csv`.
- **Siguiente paso:** recuperación de texto completo sobre los 370
  Included (mismo flujo que AIBIO_GU: catálogo RIS → Paperpile →
  `ingest_paperpile_full_texts.py` → Unpaywall/Europe PMC → BrowserOS manual
  para los que fallen automáticamente).

## 2026-09-21 — Instrucciones FT R2 alineadas al consenso T/A y enviadas

- El handoff de cribado a texto completo para R2 (`LEEME-instrucciones-FT-R2.md`
  y `screening/exports/2026-09-21__HANDOFF-cribado-FT-R2-Britos.md`) incorpora
  la regla de **vínculo analítico** (no declarativo) acordada el 2026-09-16,
  y los mismos clusters de consenso, para que R1 y R2 la apliquen sobre el
  PDF. No se cambia el protocolo escrito; se opera con la política ya
  adoptada en T/A.
- Enviado a Paola el 2026-09-21 (zip + enlace Drive a los PDF).

## 2026-09-28 — Consenso FT: 47 conflictos resueltos

- R1 (Alcides) 229 Included / 42 Excluded; R2 (Paola, planilla corregida)
  232 Included / 39 Excluded; kappa 0,32; 47 conflictos.
- **Reglas de resolución (sin cambiar el protocolo escrito):**
  1. Complicaciones del tratamiento quirúrgico (SSI, fuga, demora de
     ileostomía, diarrea post-cierre) y toxicidad de quimioterapia →
     `Included` (categoría explícita del protocolo / piloto).
  2. MSI/dMMR/KRAS predichos por IA → `Included` (cluster T/A). El
     encuadre del paper como «triage previo a IHC/PCR» o la ausencia de
     cohorte solo metastásica **no** anulan la inclusión.
  3. Preprints, pósters o PDF idénticos a un artículo ya en el corpus →
     `Excluded` / `wrong publication type` (se conserva la versión
     publicada).
  4. Pronóstico puro (supervivencia, recurrencia) sin comparar
     tratamiento ni cuantificar beneficio diferencial → `Excluded` /
     `wrong outcome`.
  5. Sin clasificador de IA/ML (ANOVA, regresión logística clásica
     univariante, solo cuantificación celular + Cox convencional) →
     `Excluded` / `wrong intervention`.
  6. Clasificación circular del TRG sobre la pieza ya graduada, o
     predicción solo in silico (GDSC) sin cohorte CCR tratada →
     `Excluded` / `wrong outcome`.
- **Consenso final:** 271 evaluados → **227 Included / 44 Excluded**.
  Importado a SQLite. Los 99 no recuperados quedan fuera del cribado FT.

## 2026-09-28 — Appraisal: proceso y andamiaje

- Decisión: con n=227, doble appraisal completo no es sostenible ni
  necesario para una revisión integrativa. Se adopta **escala R1 +
  verificación R2 por muestreo estratificado (~10–15 %)**, con el cribado
  doble (T/A + FT) como ancla de rigor. Documentado en el protocolo.
- Andamiaje: `risk_of_bias/AICRC_TX_appraisal_codebook.md` (adaptado de
  AIBIO_GU), `AICRC_TX_appraisal_BLANK_227.csv`,
  `AICRC_TX_appraisal_PILOT_20.csv` (estratificado por eje terapéutico).
- **Siguiente:** cerrar el piloto n=20, congelar calibración, escalar R1.

## 2026-09-28 — Piloto appraisal cerrado; codebook congelado

- n=20 estratificado por eje terapéutico; 4 lotes A–D; fusión sin
  correcciones mecánicas a overall MMAT/PROBAST/TRIPOD.
- Señales alineadas a AIBIO_GU: PROBAST casi universal; MMAT high raro
  (aquí 2/20 con validación multicéntrica genuina); Analysis high
  frecuente. Documentado en `risk_of_bias/AICRC_TX_appraisal_PILOT_SUMMARY.md`.
- Codebook congelado. Escala R1 autorizada.

## 2026-09-28 — Escala R1 appraisal cerrada (227/227)

- 7 lotes paralelos (207) + piloto (20). Fusión sin correcciones mecánicas.
- Distribución: MMAT low 170 / moderate 48 / high 9; PROBAST high 186 /
  unclear 37 / low 4; TRIPOD partial 130 / adequate 86 / inadequate 11;
  applies_prediction=yes 227/227; cannot_appraise 0.
- Muestra R2 estratificada n=31 (~13.7 %) preparada (ciega + clave R1).
  Detalle en `risk_of_bias/AICRC_TX_appraisal_SCALE_SUMMARY.md`.

## 2026-09-30 — Paola responde: opción A aceptada con condiciones

- Paola acepta la opción A (re-pasar aplicabilidad en los 227), la retirada
  de “más conservador gana” y 000395 → moderate (motivo: no ajusta esquema
  de quimioterapia; no el barrido de sigma). 000476 sin cambios; 000510 y
  000823 fuera de aplicabilidad (regla 5).
- Pide: reglas 6–7 del codebook original, definición escrita de `unclear`,
  esperar mayor volumen de cambios por la regla 4, kappa nuevo con
  re-puntaje ciego de la columna en los 31, y decidir 000512 en voz alta.
- Borrador de reglas v2: `risk_of_bias/AICRC_TX_applicability_rules_v2_DRAFT.md`
  (no congelado). Respuesta a Paola en borrador de Gmail.
- **Siguiente:** OK de Paola a las reglas → congelar → re-puntaje ciego de
  31 (R1 y R2) → re-pase 227 → consenso → FULL_227 → SQLite.

## 2026-10-01 — Reglas v2 congeladas; muestra nueva para el kappa

- Paola da el OK y propone redacción para H1, H3, H4, U1 y U2; se adoptan
  tal cual. Reglas congeladas en
  `risk_of_bias/AICRC_TX_applicability_rules_v2_FROZEN.md`. 000476 se mantiene
  en high (ocho high fuera de muestra, no siete).
- Kappa: muestra nueva de 25 de los 196 no tocados (estratificada
  proporcional por eje, semilla 20261001), porque 9 de los 31 (000026,
  000049, 000244, 000447, 000510, 000512, 000712, 000752, 000823) fueron
  discutidos entre R1 y R2. Esos 9 se reportan aparte como verificación de
  la regla, no como acuerdo.
- Advertencia declarada a Paola: con v1, los 25 son 24 low / 1 unclear /
  0 high (prevalencia desbalanceada). Se reportará kappa junto a acuerdo
  crudo y por categoría. Ofrecida la opción de rearmar la muestra con
  radiómica donde H4 podría cambiar el juicio.
- Clave R1 de los 25: `risk_of_bias/AICRC_TX_applicability_NEWSAMPLE_25_R1KEY.csv`
  (no se abre hasta puntuar R1 de forma independiente).
- Paquete enviado a Paola por Gmail (sin adjunto; referencia al expediente
  y a `screening/exports/paquete-R2-britos-aplicabilidad-2026-10-01.zip`).
- **Siguiente:** planilla de Paola → R1 puntúa los 25 sin ver R2 → kappa →
  re-pase 227 con registro de la regla por cambio → consenso → FULL_227 →
  SQLite.
- **Pausa** hasta la respuesta de Paola. No tocar FULL_227 ni el CSV de
  consenso.
