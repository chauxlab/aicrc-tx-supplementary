# AICRC_TX — Reglas operativas de aplicabilidad v2 (CONGELADAS)

Estado: **congelado el 2026-10-01** con el OK de Paola Britos (correo del
2026-10-01) y la redacción de H1, H3, H4, U1 y U2 propuesta por ella.
Reemplaza la señal de aplicabilidad del codebook §3; H6–H7 son las dos
señales originales del codebook. Cualquier cambio posterior exige v3 y
repetir el re-pase completo.

Columna afectada: `probast_overall_applicability` (low | high | unclear).
Pregunta: ¿el modelo, como se desarrolló/validó, informa una decisión
terapéutica en CCR?

## Regla de asignación

1. **high concern** si se cumple **al menos una** de las reglas H1–H7.
2. **unclear** si ninguna H se cumple, pero la aplicabilidad no puede
   juzgarse por información ausente o ambigua (regla U).
3. **low concern** en los demás casos.

Orden: evaluar H1–H7 → U → low. No desempatar por "más conservador".

## Reglas high concern

- **H1.** El biomarcador o la acción (p. ej. KRAS → anti-EGFR) exige un
  contexto clínico, típicamente metastásico, que la cohorte no garantiza;
  o la cohorte mezcla contextos en los que la decisión que el paper dice
  informar no es la misma decisión, y los resultados no se reportan
  separados por contexto.
- **H2.** El desenlace de decisión (p. ej. respuesta a inmunoterapia)
  depende de una elegibilidad no medida (MSI-H/dMMR) y el paper la omite.
- **H3.** Los predictores o el desenlace no están disponibles en el momento
  de la decisión clínica que el paper dice informar, ya sea porque son
  posteriores a ese momento (p. ej. H&E post-resección para decidir
  neoadyuvancia) o porque no son obtenibles en la práctica de rutina en ese
  momento (p. ej. transcriptoma completo).
- **H4.** El núcleo del trabajo es descubrimiento o mecanismo más que
  predicción aplicable; o la evidencia principal del nexo terapéutico es in
  silico o en líneas celulares; o el propio paper se declara prueba de
  concepto / no desplegable. Un "trabajo futuro" genérico en la discusión no
  cuenta.
- **H5 (exclusión).** EPV bajo, falta de validación externa y overfitting
  **no** cuentan para aplicabilidad (van a dominio de análisis / RoB
  global), salvo que además se cumpla H1–H4, H6 o H7.
- **H6.** Desenlace solo pronóstico, sin nexo terapéutico analizado
  (codebook original).
- **H7.** Cohorte mixta multi-cáncer sin resultados de CCR separables
  (codebook original).

(H5 es regla de exclusión, no de asignación: se numera así para conservar
el orden 1–5 acordado en el correo del 2026-09-29.)

## Regla unclear (U)

Poner `unclear` solo si, tras leer el PDF, se cumple alguna:

- **U1.** El reporte no permite saber si el contexto clínico, el momento de
  decisión o la elegibilidad (H1–H7) se cumplen, y eso es lo que hay que
  juzgar (p. ej. 000823: reporte tan pobre que no se puede evaluar).
- **U2.** El texto del paper es internamente ambiguo (afirmaciones
  contradictorias) y sostiene tanto low como high. Al citar U2 hay que
  transcribir en la justificación las dos lecturas.
- **U3.** El uso terapéutico es solo implícito (el paper no dice qué
  decisión informa) sin que H6 se cumpla con claridad.

`unclear` **no** es un punto medio por cautela: si una H se cumple, es
high. Toda asignación `unclear` exige citar U1, U2 o U3 en el racional.

## Protocolo de re-pase

1. Reglas congeladas (este documento).
2. Kappa sobre la definición nueva en una **muestra nueva de 25** de los 196
   no tocados (estratificada proporcional por eje, semilla 20261001; ver
   `AICRC_TX_applicability_NEWSAMPLE_25_*.csv`). R1 y R2 puntúan solo
   `probast_overall_applicability` (+ `rule_cited`), de forma independiente
   y ciega. Los 31 originales no se usan para el kappa porque 9 de ellos
   (000026, 000049, 000244, 000447, 000510, 000512, 000712, 000752, 000823)
   fueron discutidos entre R1 y R2 antes del re-puntaje; se reportan aparte
   como verificación de la regla, no como acuerdo.
3. R1 re-pasa la columna en los 227; cada cambio respecto a v1 se registra
   con la regla que lo causa (H1–H7/U1–U3). La muestra de 25 se puntúa antes
   de que R1 conozca las asignaciones de R2.
4. **Expectativa previa, declarada antes de ver el volumen:** H4 puede
   mover muchos papers de radiómica (hoy 211/227 low, 2 high, 14 unclear).
   El volumen no justifica aflojar la regla.
5. No mezclar definiciones: no se parchea solo la muestra.

## Decisiones individuales asociadas

- 000395: MMAT moderate (c4 = cant_tell por **no ajustar el esquema de
  quimioterapia concomitante**; el barrido de sigma es sobreajuste y ya se
  penaliza en PROBAST análisis). Anotar: Tabla 1, VS1 encabeza 39 pacientes
  pero TRG/quimioterapia suman 34 (5 sin explicar).
- 000476: se mantiene (sin c4 de escáner). 000510, 000823: fuera de
  aplicabilidad por H5.
- 000244: high por H6 (2yDFS fundamentalmente pronóstico) — a verificar en
  el re-pase.
- 000512: circularidad génica = RoB (concedido); mezcla primario/metastásico
  sin subanálisis = aplicabilidad vía H1 ampliada.
- La muestra R2 queda sin MMAT high; lo hacen explícito el reporte, con los
  high fuera de muestra: 000120, 000146, 000184, 000295, 000466, 000471,
  000809 y 000476 (ocho en total; 000476 se mantiene).
