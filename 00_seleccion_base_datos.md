# Introducción, Selección de la base de datos y propósito del proyecto

Este documento presenta el primer entregable del Proyecto de Investigación de la asignatura [MACHINE LEARNING_202630_NRC 5646](https://cursos.uninorte.edu.co/d2l/home/177738).

**Objetivo del entregable:** establecer las bases del proyecto mediante (i) la selección de una base de datos pertinente; (ii) un análisis exploratorio de datos (EDA) riguroso y exhaustivo; y (iii) la implementación de un modelo base como referencia inicial, comparado con una línea base trivial.

## Contexto académico e institucional

En la Universidad del Norte, algunos programas de pregrado, como Ingeniería, Administración y otros afines, ofrecen este curso corto unas semanas antes del inicio de clases. Este curso es conocido internamente como **nivelatorio de precálculo**, su propósito es que los estudiantes alcancen una base mínima para afrontar las asignaturas de matemáticas de su primer semestre. Se busca así contribuir a un mejor desempeño en la primera evaluación parcial de esas asignaturas.

Los contenidos y la meta de aprendizaje del nivelatorio se definen de acuerdo con los requerimientos de cada programa. Como los temas siguen una secuencia, el avance registrado permite identificar hasta qué punto ha llegado cada estudiante respecto de la meta establecida.

El proceso se apoya en la plataforma ALEK, que evalúa los conocimientos al inicio, propone un plan de trabajo individual y realiza evaluaciones durante el curso para ajustar ese plan. Al finalizar, aplica una evaluación de salida. El curso también cuenta con un profesor y con estudiantes universitarios que participan como tutores. En conjunto, los registros de ALEK permiten describir el nivel de entrada y de salida, los temas aprendidos y el tiempo de trabajo en la plataforma, entre otros aspectos del proceso.

Las evaluaciones de entrada y salida registran el dominio de los estudiantes en distintos temas de cálculo. En conjunto, el nivelatorio busca proporcionar fundamentos matemáticos que apoyen el desempeño y la aprobación de las asignaturas cursadas durante el primer semestre.

Las asignaturas relacionadas con este curso son las siguientes:

| Código | Asignatura |
| --- | --- |
| `MAT1011` | Álgebra y Trigonometría |
| `MAT1100` | Cálculo I (ANEC) |
| `MAT1101` | Cálculo I |
| `MAT4190` | Matemáticas Fundamentales |

La base de datos que se usará en este proyecto registra información del **nivelatorio de precálculo** durante los periodos académicos comprendidos entre 2023 y 2025.

## Propósito del proyecto

El objetivo es explorar la información académica generada durante el curso de precálculo y construir una primera aproximación para predecir el resultado de cada estudiante en el primer parcial de la asignatura relacionada. La variable objetivo es `nota_primer_parcial`, y se consideran como posibles predictores los resultados de las evaluaciones de entrada y salida, el avance en la plataforma ALEK, el tiempo de trabajo, los resultados de la prueba Saber 11, el programa académico, la división y la asignatura relacionada.

La predicción debe entenderse como una herramienta de análisis académico. Antes de utilizarla en un escenario operativo, es necesario verificar que cada variable esté disponible en el momento en que se desea realizar la predicción y que no incorpore información posterior al primer parcial.

## Estado original de la base de datos

La fuente original utilizada para el proceso de preparación es `Data/Datos_Brutos.csv`. El archivo conserva el registro detallado proveniente del curso, con identificadores anonimizados, información académica, resultados de la plataforma ALEK, asignatura relacionada y calificaciones.

| Característica | Estado observado |
| --- | --- |
| Periodos | `202310`, `202330`, `202410`, `202430`, `202510` y `202530` |
| Registros originales | 1.527 |
| Columnas originales | 31 |
| Estudiantes identificados | 1.506 |
| Filas duplicadas completas | 0 |
| Registros con `nota primer parcial` disponible | 1.497 |
| Registros sin `nota primer parcial` | 30 |
| Separador | `; ` |
| Codificación | UTF-8 con BOM |

Con esta cantidad de observaciones no se alcanza el mínimo de 20.000 registros solicitado para el proyecto. Además, para el aprendizaje supervisado solo se pueden utilizar las filas que tienen valor en la variable objetivo. Por esta razón, el conjunto original requiere un proceso controlado de aumentación de datos.

## Esquema estandarizado para el modelado

El archivo `cabeceras.csv` se utiliza como referencia del formato final y de los nombres estandarizados de las columnas. No contiene registros de estudiantes; su función en este flujo es definir el esquema de salida que utilizarán los notebooks de análisis y modelado.

En particular, los nombres de las columnas objetivo se normalizan de la siguiente manera:

| Fuente original | Esquema estandarizado |
| --- | --- |
| `nota primer parcial` | `nota_primer_parcial` |
| `nota final` | `nota_final` |

El esquema final contiene 17 columnas:

`Icfes_nuevo`, `Icfes_Matematicas`, `Tiempo_empleado_en_la_verificación_de_conocimientos`, `Dominio_Inicial`, `Categoria_temas_inicial`, `avance_precalculo_aleks`, `Dominio_Final`, `Categoria_temas_final`, `Tiempo_Total_Tiempo`, `Tiempo_total_Aprendidos_hora`, `programa_en_matricula`, `division_en_matricula`, `Mat_Curso_Asignatura_Relacionada`, `avance_cuartiles`, `cumplimiento_temas`, `nota_primer_parcial` y `nota_final`.

La columna `nota_final` se conserva en los archivos preparados para facilitar la revisión de resultados, pero no debe utilizarse como predictor de `nota_primer_parcial` debido al riesgo de fuga de información.

## Descripción de las variables del esquema final

La siguiente tabla resume el significado operativo de las 17 columnas que se conservan en el esquema estandarizado.

| Variable | Explicación |
| --- | --- |
| `Icfes_nuevo` | Puntaje global del estudiante en la prueba Saber 11 reportado por la institución. |
| `Icfes_Matematicas` | Puntaje del estudiante en el componente de Matemáticas de la prueba Saber 11. |
| `Tiempo_empleado_en_la_verificación_de_conocimientos` | Tiempo empleado por el estudiante, expresado en horas, en la verificación inicial de conocimientos de la plataforma ALEK. |
| `Dominio_Inicial` | Cantidad de temas dominados por el estudiante como conclusión de una prueba inicial (pretest) realizada en la plataforma ALEK. |
| `Categoria_temas_inicial` | Categoría descriptiva que resume el nivel o la cantidad de temas dominados al inicio. |
| `avance_precalculo_aleks` | Diferencia entre la cantidad de temas al inicio y la cantidad de temas al final del proceso en la plataforma ALEK. |
| `Dominio_Final` | Cantidad de temas alcanzados luego del curso nivelatorio, verificados mediante una prueba final (posttest) en la plataforma ALEK. |
| `Categoria_temas_final` | Categoría descriptiva del nivel o la cantidad de temas alcanzados en la medición final. |
| `Tiempo_Total_Tiempo` | Tiempo total de trabajo del estudiante en la plataforma ALEK, expresado en horas. |
| `Tiempo_total_Aprendidos_hora` | Tasa de temas aprendidos por hora, calculada o registrada para el estudiante. |
| `programa_en_matricula` | Programa académico en el que estaba matriculado el estudiante. |
| `division_en_matricula` | División académica a la que corresponde el programa matriculado por el estudiante. |
| `Mat_Curso_Asignatura_Relacionada` | Código de la asignatura relacionada con el curso de precálculo. |
| `avance_cuartiles` | Categoría por cuartiles generada por asignatura y por semestre, relativa a la distribución del grupo correspondiente. |
| `cumplimiento_temas` | Indicador categórico de si el estudiante alcanzó la meta de temas definida para el curso. |
| `nota_primer_parcial` | Calificación obtenida por el estudiante en el primer parcial de la asignatura relacionada. Es la variable objetivo del modelado. |
| `nota_final` | Calificación final de la asignatura relacionada. Se conserva para auditoría, pero no debe utilizarse como predictor de `nota_primer_parcial` por ser un resultado posterior y posible fuente de fuga. |

## Necesidad y estrategia de aumentación

El conjunto original contiene 1.527 registros, por debajo del mínimo de 20.000 observaciones establecido para el proyecto. Por esta razón, se requiere aplicar un proceso de aumentación de datos antes del análisis y el modelado.

El flujo implementado genera un conjunto aumentado de 30.000 registros: 24.000 para entrenamiento y 6.000 para prueba. Así se supera el mínimo solicitado y se conserva una partición independiente para evaluar los modelos.

La aumentación no representa nuevos estudiantes observados directamente: genera variantes sintéticas a partir de los registros disponibles, conserva el esquema de 17 columnas y aplica perturbaciones controladas a variables numéricas y calificaciones. Para reducir el riesgo de contaminación entre entrenamiento y prueba, primero se separan los estudiantes y luego se aumenta cada partición de forma independiente. Se excluyen del flujo supervisado las filas sin `nota_primer_parcial`; al final se validan el formato, los rangos, los duplicados y la separación entre particiones. El procedimiento se describe en el notebook [Generación reproducible del dataset aumentado](precalculo_generar_split_augmented_2.ipynb).

## EDA: análisis exploratorio de datos

El notebook [EDA comprehensivo](precalculo_eda_comprehensivo.ipynb) caracteriza la calidad y estructura de los datos antes del modelado. Explora la distribución de la nota del primer parcial y de las variables predictoras, sus asociaciones y posibles redundancias, además de revisar valores extremos, grupos y riesgos de fuga. Las decisiones exploratorias se calculan sobre entrenamiento y el conjunto de prueba se mantiene reservado; este notebook no entrena modelos.

Conclusiones principales:

- Se identificaron 58 registros de entrenamiento con `Tiempo_total_Aprendidos_hora = 0` pese a mostrar un aumento entre `Dominio_Inicial` y `Dominio_Final`. Se excluyeron del análisis, que continuó con 23.942 registros válidos.
- La variable objetivo se conserva en su escala original; su distribución no mostró una necesidad clara de transformarla. Los valores extremos válidos se mantienen y no se eliminan automáticamente.
- `nota_final` se excluye de las variables predictoras porque corresponde a un resultado posterior al primer parcial. Los faltantes observados se concentran en medidas de tiempo y deberán tratarse dentro del flujo de preprocesamiento.

## Modelo lineal base

El notebook [Regresión lineal base](precalculo_regresion_lineal.ipynb) prepara las variables dentro de un `Pipeline` y compara una referencia que predice la nota media con un modelo `LinearSVR` de formulación lineal. Evalúa el desempeño mediante validación cruzada y una partición de prueba reservada, e incluye intervalos bootstrap, análisis de residuos, curva de aprendizaje e interpretación de coeficientes. El preprocesamiento imputa faltantes, transforma y escala las variables numéricas, conserva sus valores extremos válidos y codifica las variables categóricas.

En la última ejecución guardada, se evaluaron 5.963 registros válidos del conjunto de prueba. Las métricas fueron:

| Modelo | RMSE | MAPE | R² | MAE |
| --- | ---: | ---: | ---: | ---: |
| Dummy (media) | 1,1282 | 37,7330 % | ≈ 0 | 0,9705 |
| SVM lineal | 0,9093 | 27,1489 % | 0,3503 | 0,7329 |

El intervalo bootstrap del 95 % para el SVM fue [0,8929; 0,9235] en RMSE, [26,2389 %; 28,0514 %] en MAPE y [0,3275; 0,3715] en R². `LinearSVR` no impone límites a las notas predichas, por lo que algunas podrían quedar fuera del rango observado de 0,5 a 5,0.

Conclusiones principales:

- El SVM lineal reduce el RMSE en 19,40 % y el MAPE en 28,05 % frente a la Dummy. Su R² de 0,3503 y MAE de 0,7329 indican una capacidad predictiva moderada: es una línea base útil, pero no un modelo de alta precisión.
- En validación cruzada en set de entrenamiento obtuvo RMSE medio de 0,8001 y R² de 0,4981, mejores que en el test. Esta brecha aconseja cautela al estimar su generalización. La falta del identificador de estudiante en el esquema también impide construir una validación agrupada dentro del entrenamiento.
- Los residuos se apartan de la normalidad y muestran heterocedasticidad (p < 0,001 en ambas pruebas); la asociación entre el valor predicho y el error absoluto es negativa (ρ = -0,1666). Esto aconseja cautela al interpretar los coeficientes y los intervalos, y refuerza el uso del modelo como referencia predictiva, no como herramienta de inferencia.
