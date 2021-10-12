
## Introducción

El presente documento tiene 3 objetivos: 1) entregar la información
necesaria para utilizar los datasets de entrenamiento para CAENES y
CIUO; 2) enumerar las instrucciones para ejecutar las rutinas y replicar
el ajuste de los modelos; 3) describir la manera en la que debe
utilizarse la API, que contiene los modelos entrenados por el equipo del
proyecto estratégico de Servicios Compartidos.

## Descripción de los datos

Los datos que se utilizaron para entrenar los modelos se encuentran
almacenados en los siguientes archivos:

  - src/data/train\_datasets/auditado\_ene\_long.feather: corresponde a
    los datos de CAENES
  - src/data/train\_datasets/auditado\_ciuo.feather: corresponde a los
    datos de CIUO

**Dataset CAENES**

En el caso de CAENES, el etiquetado de los datos proviene de 3 fuentes.
La primera de dichas fuentes corresponde al trabajo realizado por un
equipo de codificadores en el marco de las actividades del Proyecto
Estratégico de Servicios Compartidos. Dicho equipo etiquetó datos
provenientes de la coyuntura ENE entre los meses de junio y noviembre de
2020.

La segunda fuente de etiquetado corresponde al control de calidad
realizado por el Departamento de Estudios del Trabajo (DET) en el marco
de la coyuntura de la Encuesta de Empleo. Debido a que estos datos ya
habían sido revisados por codificadores entrenados, pasaron directamente
a formar parte del dataset de entrenamiento.

En tercer lugar, en una proporción mucho menor, fueron agregados algunos
datos de la coyuntura ENE del año 2018, con el objetivo de aumentar
algunas categorías con poca prevalencia.

Cabe mencionar que de las tres fuentes de información, la más importante
proviene del etiquetado realizado por el Proyecto Estratégico de
Sevicios Compartidos.

A continuación se describen las variables más relevantes del archivo:

  - idrph: identificador de persona
  - glosa\_caenes: descripción de la actividad económica
  - cod\_final: código caenes a dos dígitos
  - origen: procedencia del etiquetado. Las categorías son ene y sscc
    (Servicios Compartidos)
  - levantamiento: indica si el levantamiento fue mediante papel o
    dispositivo electrónico. Las categorías son: papi y capi
  - tiene\_auditoria: fue auditado por el equipo de Nomenclatura. Las
    categorías son 0 y 1.
  - tiene\_rev\_cruzada: el caso tuvo revisión cruzada. Las categorías
    son 0 y 1.
  - variable: indica la pregunta del cuestionario de la cuál fue
    obtenido el dato. Las categorías son c5, c9 y d5.

Debido a que cada persona puede tener más de un registro, la manera de
generar un identificador único es mediante las columnas **idrph, mes y
variable**.

**Dataset CIUO**

En el caso de CIUO, el etiquetado de los datos proviene de 2 fuentes. La
primera de ellas corresponde al trabajo realizado por un equipo de
codificadores en el marco de las actividades del Proyecto Estratégico de
Servicios Compartidos. Dicho equipo etiquetó datos provenientes de la
coyuntura ENE entre los meses de junio y noviembre de 2020, y en menor
medida, datos de la encuesta piloto EPF, realizada durante el segundo
semestre de 2020.

La segunda fuente de etiquetado corresponde al control de calidad
realizado por el Departamento de Estudios del Trabajo (DET) en el marco
de la coyuntura de la Encuesta de Empleo. Debido a que estos datos ya
habían sido revisados por codificadores entrenados, pasaron directamente
a formar parte del dataset de entrenamiento.

A continuación se describen las variables más relevantes del archivo:

  - idrph: identificador de persona
  - b1\_1: oficio
  - b1\_2: tarea
  - cod\_final: código asignado a 2 dígitos
  - encuesta: ENE o piloto EPF
  - origen: procedencia del etiquetado. Las categorías son ene y sscc
    (Servicios Compartidos)
  - levantamiento: indica si el levantamiento fue mediante papel o
    dispositivo electrónico. Las categorías son: papi y capi
  - tiene\_auditoria: fue auditado por el equipo de Nomenclatura. Las
    categorías son 0 y 1.
  - tiene\_rev\_cruzada: el caso tuvo revisión cruzada. Las categorías
    son 0 y 1.

Debido a que cada persona puede tener más de un registro, la manera de
generar un identificador único es mediante las columnas **idrph, mes**.

**Separación train-test**

Con el objeto de facilitar el desarrollo de modelos que funcionen de
manera más eficiente, se pone a disposición la partición que fue
utilizada durante en el entrenamiento. En el directorio
*src/data/split\_train\_test* se encuentran los siguientes archivos:

  - *test.feather*: set de testeo para CAENES
  - *test\_ciuo.feather*: set de testeo para CIUO
  - *train.feather*: set de entrenamiento para CAENES
  - *train\_ciuo.feather*: set de entrenamiento para CIUO

## Descripción de las rutinas

Las archivos ubicados en el directorio scripts/entrenamiento contienen
rutinas para llevar a cabo diferentes tipos de entrenamiento. En total,
fueron testeadas 4 metodologías, que consideran diferentes arquitecturas
de redes neuronales y de vectorización de textos:

  - red feed-forward y vectorización de textos mediante TF-IDF
  - red feed-forward y vectorización basada en secuencias
  - red feed-forward y vectorización enriquecida con word embedings
  - red Gated Recurrent Unit (GRU) enriquecida con word embeddings

Dado que los ejercicios consideran CAENES y CIUO, y debido a que fue
necesario entrenar modelos para 1 y 2 dígitos, el número total de
rutinas de entrenamiento es 16, vale decir, 8 para cada clasificador.
Dentro de dichas 8 rutinas, 4 corresponden a ejercicios realizados a un
dígito y 4 a dos dígitos.

Además, el directorio contiene rutinas para la edición de datos y para
la implementación de procesos de entrenamiento y testeo.

A continuación, se describe el orden en el que deben ejecutarse los
programas, para reproducir los resultados:

**Entrenamiento CAENES**

1.  edicion\_particion.R: editar textos y dividir el dataset en testeo y
    entrenamiento.

2.  general\_training.R: llevar a cabo el entrenamiento para los 16
    modelos ajustados. Notar que este archivo contiene el entrenamiento
    para los modelos de CIUO y CAENES. Para ejecutar este script, es
    necesario cargar un archivo que requiere, al menos, 16 gigas de
    memoria RAM. Dicho modelo está disponible en
    <https://github.com/dccuchile/spanish-word-embeddings> con el nombre
    de Binary formar (.bin)

3.  general\_testing.R: llevar a cabo predicciones e identificar el
    rendimiendo en el set de testeo.

**Entrenamiento CIUO**

1.  edicion\_particion\_ciuo.R: editar textos y dividir el dataset en
    testeo y entrenamiento.

2.  general\_training.R: llevar a cabo el entrenamiento para los 16
    modelos ajustados. Notar que este archivo contiene el entrenamiento
    para los modelos de CIUO y CAENES. Para ejecutar este script, es
    necesario cargar un archivo que requiere, al menos, 16 gigas de
    memoria RAM. Dicho modelo está disponible en
    <https://github.com/dccuchile/spanish-word-embeddings> con el nombre
    de Binary formar (.bin)

3.  general\_testing\_ciuo.R: llevar a cabo predicciones e identificar
    el rendimiendo en el set de testeo.

**Rendimiento de los modelos**

Los cuadros siguientes resumen los resultados obtenidos en el set de
testeo para cada una de las estrategias y desagregaciones realizadas. Es
importante mencionar que los modelos fueron entrenados con un
subconjunto de los datos de entrenamiento, ya que solo se utilizaron
aquellos **etiquetados por codificadores de SSCC** \[1\].

En el caso de CAENES no se advierten diferencias importantes entre las
distintas extrategias elegidas, sin embargo, vale la pena mencionar que
tanto a uno como a dos dígitos, la vectorización mediante secuencias
(seq\_1d y seq\_2d) muestra un desempeño ligeramente más alto que los
demás modelos. Esto se debe a que en la extracción de características
para esa estrategia, se utilizó la información de la variable CISE. Pese
a ello, se recomienda que el modelo basado en *word embeddings* sea
utilizado de manera productiva, ya que el hecho de enriquecer la
descripción de los textos mediante un modelo entrenado con grandes
cantidades de texto, hace suponer que las predicciones sean menos
sensibles a cambios en la manera de registrar la información \[2\].

En el caso de CIUO es un poco más evidente que el modelo que utiliza
*word embeddings* presenta un mejor rendimiento que los demás, por lo
que, nuevamente, se sugiere que sea utilizado de manera productiva.

<table class=" lightable-classic" style='font-family: "Arial Narrow", "Source Sans Pro", sans-serif; width: auto !important; margin-left: auto; margin-right: auto;'>

<caption>

<center>

<strong>Resultados CAENES 1 dígito</strong>

</center>

</caption>

<thead>

<tr>

<th style="text-align:left;">

modelo

</th>

<th style="text-align:right;">

acc

</th>

<th style="text-align:right;">

macro

</th>

<th style="text-align:right;">

micro

</th>

<th style="text-align:right;">

weighted

</th>

</tr>

</thead>

<tbody>

<tr>

<td style="text-align:left;">

seq\_1d

</td>

<td style="text-align:right;">

0.9384

</td>

<td style="text-align:right;">

0.8757

</td>

<td style="text-align:right;">

0.9384

</td>

<td style="text-align:right;">

0.9386

</td>

</tr>

<tr>

<td style="text-align:left;">

tfidf\_1d

</td>

<td style="text-align:right;">

0.9327

</td>

<td style="text-align:right;">

0.8658

</td>

<td style="text-align:right;">

0.9327

</td>

<td style="text-align:right;">

0.9330

</td>

</tr>

<tr>

<td style="text-align:left;">

emb\_simple\_1d

</td>

<td style="text-align:right;">

0.9274

</td>

<td style="text-align:right;">

0.8641

</td>

<td style="text-align:right;">

0.9274

</td>

<td style="text-align:right;">

0.9280

</td>

</tr>

<tr>

<td style="text-align:left;">

emb\_gru\_1d

</td>

<td style="text-align:right;">

0.9327

</td>

<td style="text-align:right;">

0.8694

</td>

<td style="text-align:right;">

0.9327

</td>

<td style="text-align:right;">

0.9328

</td>

</tr>

</tbody>

</table>

<table class=" lightable-classic" style='font-family: "Arial Narrow", "Source Sans Pro", sans-serif; width: auto !important; margin-left: auto; margin-right: auto;'>

<caption>

<center>

<strong>Resultados CAENES 2 dígitos</strong>

</center>

</caption>

<thead>

<tr>

<th style="text-align:left;">

modelo

</th>

<th style="text-align:right;">

acc

</th>

<th style="text-align:right;">

macro

</th>

<th style="text-align:right;">

micro

</th>

<th style="text-align:right;">

weighted

</th>

</tr>

</thead>

<tbody>

<tr>

<td style="text-align:left;">

seq\_2d

</td>

<td style="text-align:right;">

0.9075

</td>

<td style="text-align:right;">

0.7536

</td>

<td style="text-align:right;">

0.9075

</td>

<td style="text-align:right;">

0.9083

</td>

</tr>

<tr>

<td style="text-align:left;">

tfidf\_2d

</td>

<td style="text-align:right;">

0.9076

</td>

<td style="text-align:right;">

0.7579

</td>

<td style="text-align:right;">

0.9076

</td>

<td style="text-align:right;">

0.9081

</td>

</tr>

<tr>

<td style="text-align:left;">

emb\_simple\_2d

</td>

<td style="text-align:right;">

0.9021

</td>

<td style="text-align:right;">

0.7631

</td>

<td style="text-align:right;">

0.9021

</td>

<td style="text-align:right;">

0.9039

</td>

</tr>

<tr>

<td style="text-align:left;">

emb\_gru\_2d

</td>

<td style="text-align:right;">

0.9048

</td>

<td style="text-align:right;">

0.7630

</td>

<td style="text-align:right;">

0.9048

</td>

<td style="text-align:right;">

0.9052

</td>

</tr>

</tbody>

</table>

<table class=" lightable-classic" style='font-family: "Arial Narrow", "Source Sans Pro", sans-serif; width: auto !important; margin-left: auto; margin-right: auto;'>

<caption>

<center>

<strong>Resultados CIUO 1 dígito</strong>

</center>

</caption>

<thead>

<tr>

<th style="text-align:left;">

modelo

</th>

<th style="text-align:right;">

acc

</th>

<th style="text-align:right;">

macro

</th>

<th style="text-align:right;">

micro

</th>

<th style="text-align:right;">

weighted

</th>

</tr>

</thead>

<tbody>

<tr>

<td style="text-align:left;">

seq\_1d

</td>

<td style="text-align:right;">

0.8858

</td>

<td style="text-align:right;">

0.8599

</td>

<td style="text-align:right;">

0.8858

</td>

<td style="text-align:right;">

0.8855

</td>

</tr>

<tr>

<td style="text-align:left;">

tfidf\_1d

</td>

<td style="text-align:right;">

0.8684

</td>

<td style="text-align:right;">

0.8362

</td>

<td style="text-align:right;">

0.8684

</td>

<td style="text-align:right;">

0.8686

</td>

</tr>

<tr>

<td style="text-align:left;">

emb\_simple\_1d

</td>

<td style="text-align:right;">

0.8793

</td>

<td style="text-align:right;">

0.8519

</td>

<td style="text-align:right;">

0.8793

</td>

<td style="text-align:right;">

0.8807

</td>

</tr>

<tr>

<td style="text-align:left;">

emb\_gru\_1d

</td>

<td style="text-align:right;">

0.8989

</td>

<td style="text-align:right;">

0.8796

</td>

<td style="text-align:right;">

0.8989

</td>

<td style="text-align:right;">

0.8990

</td>

</tr>

</tbody>

</table>

<table class=" lightable-classic" style='font-family: "Arial Narrow", "Source Sans Pro", sans-serif; width: auto !important; margin-left: auto; margin-right: auto;'>

<caption>

<center>

<strong>Resultados CIUO 2 dígitos</strong>

</center>

</caption>

<thead>

<tr>

<th style="text-align:left;">

modelo

</th>

<th style="text-align:right;">

acc

</th>

<th style="text-align:right;">

macro

</th>

<th style="text-align:right;">

micro

</th>

<th style="text-align:right;">

weighted

</th>

</tr>

</thead>

<tbody>

<tr>

<td style="text-align:left;">

seq\_2d

</td>

<td style="text-align:right;">

0.8456

</td>

<td style="text-align:right;">

0.7249

</td>

<td style="text-align:right;">

0.8456

</td>

<td style="text-align:right;">

0.8476

</td>

</tr>

<tr>

<td style="text-align:left;">

tfidf\_2d

</td>

<td style="text-align:right;">

0.8412

</td>

<td style="text-align:right;">

0.7355

</td>

<td style="text-align:right;">

0.8412

</td>

<td style="text-align:right;">

0.8431

</td>

</tr>

<tr>

<td style="text-align:left;">

emb\_simple\_2d

</td>

<td style="text-align:right;">

0.8324

</td>

<td style="text-align:right;">

0.7220

</td>

<td style="text-align:right;">

0.8324

</td>

<td style="text-align:right;">

0.8348

</td>

</tr>

<tr>

<td style="text-align:left;">

emb\_gru\_2d

</td>

<td style="text-align:right;">

0.8526

</td>

<td style="text-align:right;">

0.7364

</td>

<td style="text-align:right;">

0.8526

</td>

<td style="text-align:right;">

0.8543

</td>

</tr>

</tbody>

</table>

## Descripción del funcionamiento de la API

Con el objeto de que los modelos puedan ser utilizados de manera
productiva, se ha dispuesto una API que permite hacer predicciones para
CAENES y CIUO utilizando un modelo basado en *word embeddings* y una
arquitectura de red GRU.

La ventaja de la API por sobre la utilización directa de los archivos
que contienen los modelos, radica en que el usuario no requiere tener
ninguna de las dependencias necesarias para ejecutar los archivos,
disminuyendo significativamente las barreras de entrada para acceder a
los modelos. La única dependencia para utilizar la API es el paquete
`httr` de R o cualquier otra herramienta que permita llevar a cabo
solicitudes a un servidor.

En la actualidad, la API cuenta con solo un *endpoint*, que recibe 3
parámetros:

  - text: archivo json con los textos a predecir
  - classifier: tipo de clasificador para el cual se requiere la
    predicción. Las opciones son “caenes” o “ciuo”
  - digits: indica si la predicción debe hacerse a uno o dos dígitos.
    Los parámetros posibles son los valores 1 o 2.

A continuación, se presenta un ejemplo de cómo hacer una solicitud al
servidor, para que este devuelva una predicción a un dígito de CAENES.

``` r
# Predecir a un dígito
library(httr)
library(feather)
caenes <- read_feather("src/data/split_train_test/test.feather")
request <-  httr::POST("http://143.198.79.143:8080/predict",
                 encode = "json",
                 body =  list(text = caenes$glosa_caenes[1:10],
                              classification = "caenes",
                              digits = 1)
)

httr::status_code(request)
response <- httr::content(request)
```

Dentro de *body*, el único parámetro **no opcional** es *text*, dentro
del cual debe ingresarse un vector de *strings* con los textos para los
cuales se busca una predicción. En este caso se está usando un vector de
glosas de actividad económica, para obtener la clasificación CAENES a un
dígito.

Es importante mencionar que el modelo en el caso de CIUO fue entrenado
con una concatenación de oficio y tarea, de modo que es altamente
recomendable que el input tenga ese mismo formato. En caso de que sea
suministrada la información de oficio o de tarea por separado, lo
esperable es que el rendimiento sea menor al reportado en las tablas de
más arriba.

Un segundo elemento a tener en consideración es que los datos no deben
tener ningún tipo de edición antes de ser entregados como insumo a la
API, a excepción de la concatenación de oficio y tarea, en el caso de
CIUO. El motivo de ello es que debe existir consistencia entre la
edición utilizada durante el entrenamiento y la de los nuevos datos. El
usuario debe perder cuidado a este respecto, pues la API contiene una
rutina de edición que asegura la consistencia recién mencionada.

1.  La variable origen permite filtrar los datos etiquetados por el
    equipo de SSCC.

2.  Para mayores detalles sobre la construcción de *word embeddings*,
    ver el trabajo de Jorge Perez Rojas
    \[<https://github.com/dccuchile/spanish-word-embeddings>\]
