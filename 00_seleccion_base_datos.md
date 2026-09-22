# Selección de la base de datos y propósito del proyecto

## Contexto académico

La base de datos registra información del curso de precálculo de la Universidad del Norte durante los periodos académicos comprendidos entre 2023 y 2025. Este curso se desarrolla antes del inicio del semestre y busca fortalecer los conocimientos matemáticos con los que los estudiantes comienzan su formación universitaria.

Al inicio y al final del curso, los estudiantes son evaluados con respecto a su dominio de diferentes temas de cálculo. La finalidad general es proporcionar los fundamentos necesarios para favorecer el desempeño y la aprobación de las asignaturas de matemáticas que se cursan durante el primer semestre.

Las asignaturas relacionadas que aparecen en la fuente son:

| Código | Asignatura |
| --- | --- |
| `MAT1011` | Álgebra y Trigonometría |
| `MAT1100` | Cálculo I (ANEC) |
| `MAT1101` | Cálculo I |
| `MAT4190` | Matemáticas Fundamentales |

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

El conjunto original contiene 1.527 registros, por debajo del mínimo de 20.000. El proceso de este proyecto establece una meta de 30.000 observaciones, distribuidas en 24.000 registros de entrenamiento y 6.000 registros de prueba. La meta permite superar el requisito mínimo y mantener una división independiente para evaluar los modelos.

La aumentación no representa nuevos estudiantes observados directamente. Consiste en generar variantes sintéticas a partir de las observaciones disponibles, conservando el esquema de 17 columnas y aplicando perturbaciones controladas sobre variables numéricas y calificaciones. Para reducir el riesgo de fuga entre entrenamiento y prueba, la división se realiza por estudiante antes de aumentar cada partición. Las filas sin `nota_primer_parcial` se excluyen del flujo supervisado, y al final se validan el formato, los rangos, la ausencia de duplicados y la separación entre las particiones.

El siguiente documento del libro presenta la implementación reproducible de esta preparación y generación del dataset aumentado.
