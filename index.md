# Trabajo 4 Técnicas en Aprendizaje Estadístico

# **Integrantes**

- **Alejandro Ortiz Mejía**

**Nota**: Puede hacer zoom a este blog para visualizar mejor las imágenes, o puede hacer clic derecho sobre cada imagen y dar clic en "abrir imagen en una nueva pestaña" para una visualización en máxima resolución.

## **Video Promocional**

<p align="center">
<iframe width="700" height="455" src="https://www.youtube.com/embed/1H9kFkc_ONg" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</p>

## **Planteamiento del Problema** 

El RUNT significa Registro Único Nacional de Tránsito, y funciona como una gran base de datos centralizada que contiene información sobre todos los vehículos en el país [1].
Diariamente las personas acuden ante la entidad del tránsito para registrar su vehículo, así que se tienen registros del número de vehículos registrados por cada día del año; este es nuestro caso.

Predecir el número de vehículos que serán registrados en el futuro tomando como base los que se han registrado en el pasado podría ser de gran interés para las autoridades de movilidad en el país, ya que esto les permitiría entender las dinámicas de las futuras situaciones en ámbitos como la congestión vehicular o la contaminación del aire, con lo cual pueden anticiparse y tomar decisiones o planear estrategias que permitan prevenir situaciones que lleven a las ciudades al colapso.

**Objetivo**: Crear un modelo de inteligencia artificial supervisado que permita predecir el número de unidades de vehículos que serán inscritos en el RUNT en diferentes periodos de tiempo.

### **Descripción del conjunto de datos**

El conjunto de datos contiene 2192 registros de la cantidad de vehículos registrados ante el RUNT diariamente registrados cronológicamente, este conjunto tiene dos variables:

- **Fecha**: la fecha está en formato Día/Mes/Año, y contiene las fechas desde 1 de enero de 2012 hasta el 31 de diciembre de 2017 sin ningún dato faltante; es decir, 2192 días en total.

- **Unidades**: Es la cantidad de vehículos registrados en el correspondiente día.

Una **serie de tiempo** son datos estadísticos que se recopilan, observan o registran en intervalos de tiempo regulares (diario, semanal, semestral, anual, entre otros) [2]. A partir de esta definición podemos decir que nuestro conjunto de datos es una serie de tiempo.

**Número de vehículos inscritos por año**

- Año 2012: En el 2012 se inscribieron en total **311920** vehículos.

- Año 2013: En el 2013 se inscribieron en total **295803** vehículos.

- Año 2014: En el 2014 se inscribieron en total **327478** vehículos.

- Año 2015: En el 2015 se inscribieron en total **284916** vehículos.

- Año 2016: En el 2016 se inscribieron en total **254893** vehículos.

- Año 2017: En el 2017 se inscribieron en total **239497** vehículos.

**¿Cómo se ve el conjunto de datos?**

En la figura 1 se puede observar el comportamiento de la variable en el tiempo. En primera instancia, se puede notar que año tras año hay muchos picos, y en partícular, hay un gran pico que se produce siempre a final de año, a este fenómeno se le conoce como ***variación estacional***; es decir, se produce cuando en una serie de tiempo ocurren variaciones o movimientos que recurren año tras año en los mismos meses (o en los mismos trimestres) del año poco más o menos con la misma intensidad.

![image](/images/figura1.png)
**Figura 1**. Comportamiento de la variable Unidades en el tiempo.

Con el fin de tener un mejor entendimiento de cómo es el comportamiento de esta serie de tiempo, en la figura 2 se observa el comportamiento mensual de las unidades de vehículos registrados; esta vista nos permite entender de una mejor manera este comportamiento al reducir el ruido que introduce la alta variación de la inscripción de vehículos diaria de la figura 1. Acá se evidencia mejor los picos producidos al final de cada año.

![image](/images/figura2.png)
**Figura 2**.  Comportamiento de la variable Unidades en el tiempo de manera mensual.

De acuerdo con James, Witten, Hastie y Tibshirani [3, p.427], en una serie de tiempo no se puede suponer que las observaciones son independientes entre sí, más aún, los valores cercanos en el tiempo tienden a tener una autocorrelación similar entre sí. Para ser claros, se puede considerar los pares de observaciones (Ut, Ut-l), un retraso de l días (o meses). Si tomamos todos esos pares en la serie de Ut y calculamos su coeficiente de correlación, esto da la autocorrelación en el rezago l (también llamado lag l). La figura 3 muestra la función de autocorrelación para todos los rezagos (en días) hasta 31. A partir de esta, se observa una correlación considerable con 27, 21, 14, 7, 1 días de retraso, esto nos sugiere que para cada una de estas cantidades de días existe una tendencia estacional, siendo la más fuerte la de 27 días; es decir, cada 27 días la cantidad de vehículos inscritos en el RUNT tiene una intensidad relativamente similar; esta variación estacional en específico se puede estar presentado, en parte, porque las personas suelen inscribir más vehículos en el último día hábil del mes, dando como resultado que los últimos días del mes, en general, es cuando más se inscriben vehículos (ver figura 4). Por otro lado, la variación estacional presentada cada 7 días se puede estar presentando, en parte, porque los sábados y domingos suelen haber muchas menos inscripciones que los demás días de la semana (ver figura 5).

![image](/images/figura3.png)

**Figura 3**. Función de autocorrelación donde el eje x representa el número de días de retraso (lags), y el eje *y*, el coeficiente de correlación; los picos corresponden a los días 1, 7, 14, 21 y 27, respectivamente.

![image](/images/figura4.png)

**Figura 4**. Inscripción de vehículos por día del mes


![image](/images/figura5.png)

**Figura 5**. Inscripción de vehículos por día de la semana, donde 0 es el lunes; el 1, martes; el 2, miércoles; el 3, jueves; el 4, viernes; el 5, sábado; y el 6, domingo.


El anterior ejercicio también puede ser llevado a cabo tomando las unidades de vehículos inscritas de manera mensual; la figura 6 muestra la función de autocorrelación si se toma este enfoque. En esta se puede observar una clara tendencia estacional anual (línea fucsia), lo cual ratifica lo observado en la figura 2. En otras palabras, cada 12 meses la cantidad de vehículos inscritos en el RUNT tiene una intensidad relativamente similar, y este comportamiento se hace más evidente en el mes de diciembre, ya que en este mes es donde se realizan la mayor cantidad de inscripciones de vehículos (ver figura 7).


![image](/images/figura6.png)
**Figura 6**. Función de autocorrelación donde el eje x representa el número de meses de retraso (lags), y el eje *y*, el coeficiente de correlación; el pico en fucsia representa el coeficiente de correlación para un retraso de 12 meses.

![image](/images/figura7.png)

**Figura 7**. Inscripción de vehículos por mes del año.

**Creación de Variables Predictoras Derivadas**

A partir de la variable Fecha se crearon las siguientes variables derivadas:

- **Is_LBDM**: si la fecha correspondiente es el último día hábil del mes (día más cercano a final de mes que no es sábado o domingo), esta variable toma el valor de 1, de lo contrario toma el valor 0. Se toma esta variable con el fin de indicarle al modelo que en estos días en específico se suelen hacer más inscripciones de vehículos tal como se evidenció en la figura 4.

- **is_holiday**: si la fecha correspondiente es un día festivo en Colombia, esta variable toma el valor de 1, de lo contrario toma el valor 0. Esta variable se toma con el fin de indicarle al modelo que en estos días en específico no suelen haber inscripciones de vehículos.

- **day_of_week**: Esta variable indica el día de la semana, en la cual el 0 es el lunes; el 1, martes; el 2, miércoles; el 3, jueves; el 4, viernes; el 5, sábado; y el 6, domingo. Esto se realiza con el fin de indicarle al modelo que los días sábado y domingo las inscripciones de vehículos suelen ser bastante menores en relación con lo demás días de la semana (ver figura ).

- **day_of_month**: corresponde al día del mes de la fecha correspondiente. Esta variable se toma con el fin de indicarle al modelo que los primeros días del mes, por ejemplo, se inscriben menos vehículos en promedio (ver figura 6).

- **month_of_year**: Corresponde al mes del año de la fecha correspondiente. Esta variable se toma con el fin de indicarle al modelo que en el último mes del año se suelen realizar más inscripciones de vehículos en relación con los demás meses, y que en el mes de enero se suelen inscribir menos vehículos en relación con los demás meses. 

- **timestamp**: corresponde al número de segundos que han pasado desde el 1 de enero de 1970 a la media noche. Esta variable se crea con el fin de conservar el orden cronológico natural de las fechas, lo cual le permite al modelo capturar la tendencia que se viene marcando históricamente a partir de los datos de entrenamiento.


## **Modelo de predicción**

El perceptrón multicapa es una red neuronal artificial formada por múltiples capas, de tal manera que tiene la capacidad para resolver problemas que no son linealmente separables [3].

En esta red cada neurona (excepto las de entrada) realiza calculos con las entradas que recibió de la capa de neuronas anterior a ella, dichos calculos incluye la aplicación de una función de activación, que en este caso es la función *relu*; posteriormente, el resultado de una neurona es enviado a la siguiente capa en la cual se repite el mismo procedimiento hasta llegar a la capa de salida donde se realizan los últimos calculos para finalmente entregar la predicción. La arquitectura del Perceptrón Multicapa (ver figura 2) le permite aprender los diferentes patrones que hay en las imágenes.

El perceptron multicapa tiene 3 tipos de capas:

- Capa de entrada: Constituida por aquellas neuronas que introducen los patrones de entrada en la red. En estas neuronas no se produce procesamiento [3].

- Capas ocultas: Formada por aquellas neuronas cuyas entradas provienen de capas anteriores y cuyas salidas pasan a neuronas de capas posteriores [3].

- Capa de salida: Neuronas cuyos valores de salida se corresponden con las salidas de toda la red [3].

![image](/images/RedNeuronalArtificial.png)

Figura 8. Perceptrón Multicapa [3].

En este caso, el perceptrón multicapa se construyó con la siguiente arquitectura:

- **Capa de entrada**: por medio de esta se ingresa la información de cada registro, y contiene **únicamente** las variables predictoras derivadas, por tanto, solo tiene 6 neuronas en la capa de entrada.

- **Capas ocultas**: mediante un ejercicio de ensayo y error, se logró determinar que 3 capas ocultas mostraban un buen desempeño; las 3 capas tienen un tamaño de 64 neuronas cada una.

- **Capa de salida**: Dado que solamente tenemos una variable respuesta (Unidades), únicamente se tiene una neurona de salida.

- **Postprocesado**: dado que algunos valores predichos por la red neuronal llegan a ser levemente menores a cero, estos deben ser llevados a cero, es decir, si un valor predicho por la red neuronal es menor a cero, este es cambiado por el valor cero. Esto se realiza ya que los valores negativos para el contexto de la variable respuesta no tienen sentido. 

## **Resultados**

### Predicción del año 2017

Para realizar la predicción del año 2017, se entrenó el modelo con los datos del periodo desde el 1 enero de 2012 hasta el 31 de diciembre de 2016.

A continuación, se presentan los resultados obtenidos al predecir el año 2017. En la figura 7 muestra en color azul la predicción hecha por el modelo, y en negro, los datos reales; visualmente, la predicción parece ser relativamente buena, sin embargo, no logra capturar completamente bien los altos picos presentados desde la segunda mitad del año, ya que aunque estos se presentaron en cierta medida el año anterior (2016), estos no fueron tan altos como los del 2017, estos picos **podrían** deberse a un fenómeno de las series de tiempo llamado ***variación irregular***, este fenómeno se trata de una variación aleatoria y, por tanto, impredecible.


![image](/images/figura9.png)

**Figura 9**. Predicción del año 2017. En el eje x se muestra la fecha, y en el eje *y*, las unidades de vehículos. El azul es la predicción de la serie, y el negro son los valores reales. 
 

**Métricas para todo el 2017**

Para evaluar la predicción del modelo, se utilizaron las siguientes métricas:

- **RMSE**: el RMSE se calcula de acuerdo a la figura 9. Para esta métrica se obtuvo un valor de aproximadamente **219**, esto quiere decir que en promedio las predicciones se alejan del dato real en 219 unidades.

![image](/images/figura10.png)

**Figura 10**. Fórmula para calcular la raíz del error cuadrático medio (RMSE).

- **R-cuadrado**: El coeficiente de determinación (R cuadrado) se calcula de acuerdo a la figura 10. Para esta métrica se obtuvo un valor de **83 %**, esto quiere decir que el 83 % de la variabilidad en los datos es explicada por la relación entre las variables predictoras y las Unidades de vehículos.

![image](/images/figura11.png)

**Figura 11**. Fórmula para calcular el coeficiente de determinación (R cuadrado).

**Métricas para el primer semestre de 2017**

- **RMSE**: Para esta métrica se obtuvo un valor de aproximadamente **168**.

- **R-cuadrado**: Para esta métrica se obtuvo un valor de **88 %**.

### Predicción del 2018

Para predecir el 2018 se entrenó un modelo con los datos desde el 2012 hasta el 2017, es decir, todo el conjunto de datos. En la figura 11 se muestra en azul la predicción y en color negro, los datos originales.

![image](/images/figura12.png)

**Figura 12**. Predicción del año 2018. En azul se muestra la predicción, y en negro, los datos originales.

Al realizar una inspección visual, podemos notar que históricamente, se presenta una leve tendencia lineal a la baja como lo muestra la figura 13, esta tendencia es correctamente capturada por el modelo, ya que la predicción (en azul) la conserva. Es importante aclarar que la línea mostrada en la figura 13 fue ajustada manual y arbitrariamente por el autor de este blog.

![image](/images/figura13.png)

**Figura 13**. Tendencia lineal a la baja del conjunto de datos y predicción.

### Predicción del periodo 2012 hasta 2016

Para la predicción de este periodo se utiliza el mismo modelo utilizado para la predicción del año 2017, es decir, se procede a predecir el conjunto de entrenamiento. En la figura 14 se puede observa la gráfica de la predicción, donde los datos predichos están en azul; y los datos reales, en negro. Veamos que visualmente hay un relativo buen ajuste.

![image](/images/figura12.png)

**Figura 12**. Predicción del periodo entre los años 2012 y 2016. En azul se muestra la predicción, y en negro, los datos originales.

**Métricas**

- **RMSE**: Para esta métrica se obtuvo un valor de aproximadamente **250**, esto quiere decir que en promedio las predicciones se alejan del dato real en 429 unidades.

- **R-cuadrado**: Para esta métrica se obtuvo un valor de **79 %**, esto quiere decir que el 40 % de la variabilidad en los datos es explicada por la relación entre el tiempo y las unidades de vehículos.

## **Conclusiones**

Durante este informe se presentó una serie de tiempo univariada; los datos históricos de esta serie permitieron crear nuevas variables predictoras derivadas a partir de un análisis de tendencias y comportamientos presentes en los datos; estas nuevas variables permitieron entrenar y ajustar un modelo para predecir diferentes periodos. De estas predicciones, se mostró que era posible alcanzar coeficientes de determinación (R-cuadrado) entre 79 % y 88 % que, del cual podemos decir que es relativamente alto, sin embargo, las variaciones aleatorias de los datos e incluso imprecisiones en la formulación del modelo pudieron haber afectado la precisión de este.

## **Referencias**

[1] "Qué es y cómo funciona el RUNT". Inicio - Programa Servicios de Transito. https://serviciosdetransito.com/index.php/noticias/139-que-es-y-como-funciona-el-runt (accedido el 28 de agosto de 2021).

[2] Departamento de Matemáticas. http://www.estadistica.mat.uson.mx/Material/seriesdetiempo.pdf (accedido el 28 de agosto de 2021).

[3] G. James, D. Witten, T. Hastie y R. Tibshirani, *An Introduction to Statistical Learning With Applications in R*, 2a ed. New York, NY: Springer, 2021.

[4] Colaboradores de los proyectos Wikimedia. "Análisis de la regresión - Wikipedia, la enciclopedia libre". Wikipedia, la enciclopedia libre. https://es.wikipedia.org/wiki/Análisis_de_la_regresión (accedido el 31 de agosto de 2021).

[5] T. Hastie, R. Tibshirani y J. Friedman, *The Elements of Statistical Learning Data Mining, Inference, and Prediction*, 2a ed. New York: Springer, 2001.

[6] "Introduction to darts". GitHub. https://github.com/unit8co/darts/blob/master/examples/01-darts-intro.ipynb (accedido el 31 de agosto de 2021).



