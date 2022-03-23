<img src="https://bit.ly/2VnXWr2" alt="Ironhack Logo" width="100"/>

# Predicción de bajas laborales
*Carlos Romero*

*Data0921, Barcelona, 2022-03-23*

## Content
- [Project Description](#project-description)
- [Hypotheses / Questions](#hypotheses-questions)
- [Contexto](#contexto)
- [Dataset](#dataset)
- [Cleaning](#cleaning)
- [Analysis](#analysis)
- [Model Training and Evaluation](#model-training-and-evaluation)
- [Conclusion](#conclusion)
- [Future Work](#future-work)
- [Organization](#organization)
- [Links](#links)

## Project Description
El objetivo de este proyecto es el de obtener una previsión de la plantilla de baja laboral hasta final de año de un hospital público. Por falta de tiempo se ha limitado la predicción al colectivo de diplomados/as de enfermería.
* La variable objetivo es la plantilla de baja mensual
* Las variables independientes son: plantilla mensual, edad media mensual, festivos intersemanales, casos covid, búsquedas google y cambio normativa.

## Hypotheses / Questions
* ¿Cuál será la evolución de las bajas laborales del personal de enfermería hasta final de año?
* ¿Qué coste supondría para el Hospital cubrir la totalidad de las bajas con contrataciones de sustitución directa?
* ¿En relación al presupuesto de sustituciones para bajas laborales de enfermas faltará dinero o sobrará? ¿Cuánto dinero sobrará o faltará?

## Contexto
* Tanto la variable de bajas laborales como la variable de plantilla se expresan en plantilla equivalente. La plantilla equivalente se calcula dividiendo los días contratados del mes (o días de baja) partido por los días naturales del mes y multiplicado por la proporción de jornada (completa = 100%).
* Se ha estimado que el presupuesto asignado a la bolsa de sustituciones para el personal de enfermería de este año es de 1.888.965,73€
* Se ha estimado que el coste medio de la cobertura de 1 enfermera durante un mes de baja es de 2.513,58

## Dataset
El dataset utilizado en este proyecto ha sido creado utilizando los siguientes recursos:
* Datos internos privados del ICS: datos de absentismo y contratación. Se ha llevado a cabo un proceso de generación y agregación de los datos para disponer de un dataset de 2400 registros con información mensual desde el año 2003 (anteriormente se usaba otro programa y no hay registros históricos)
* Datos externos públicos: 
	- Para los casos covid se ha usado un [dataset disponible](https://analisi.transparenciacatalunya.cat/Salut/Registre-de-casos-de-COVID-19-a-Catalunya-per-rea-/xuwf-dxjd) en la web de dades obertes de Catalunya y se ha usado la API de Socrata para automatizar la actualización de datos.
	- Para las búsquedas en google de términos relacionados con la gripe se ha usado la API no oficial de Google Trends llamada pytrends. 
	- Para estimar los datos a futuro de casos covid se han importado los datos publicados en [x-y.es](https://x-y.es/covid19/sudafrica.pais).

## Cleaning
* Variable plantilla de baja: a partir de un dataset con todas las bajas laborales por trabajador se ha calculado para cada mes (desde el 01/01/03 hasta el 31/12/22) los días de baja expresados en plantilla equivalente mensual. Se han agrupado los trabajadores por colectivos.
* Variable plantilla: a partir de un dataset con todos los contratos por trabajador se ha calculado para cada mes (desde el 01/01/03 hasta el 31/12/22) los días contratados expresados en plantilla equivalente mensual. Se han agrupado los trabajadores por colectivos.
* Variable edad media: Para cada trabajador contratado se ha calculado la edad de este al finalizar el mes a partir de la fecha de nacimiento. Se han agrupado los trabajadores por colectivos y se ha calculado el promedio de edad del colectivo para cada mes.
* Se han unido las tres variables de datasets internos en un único dataset.
* Se ha creado una columna en formato datetime (date) a partir de los campo “AÑO” y “MES”.
* Se han limitado los datos a los anteriores a 01/03/2022 y se han eliminado los colectivos de médicos residentes y otros por no tener estos contratos de cobertura por baja médica.
* Variable casos covid: se ha rellenado con 0 los valores anteriores a la recogida de registros (01/03/2020).
* Variable búsquedas google: se ha creado una columna en formato datetime, se ha interpolado los datos anteriores a 01/10/05 a partir de la media del periodo 2006-2008 al considerarse que los valores eran inconsistentes con los que se observaban a porteriori.
* Variable cambio normativa: se ha rellenado con 0 los meses donde la normativa que penalizaba las bajas no estaba vigente.


## Analysis
* Análisis de correlación lineal: se ha construido una matriz de correlaciones (aplicando método spearman y Pearson) para ver la correlación lineal entre las variables independientes y la objetivo. Las variables plantilla, casos covid y normativa son las que presenta mayor correlación con la objetivo.
* Análisis de colinealidad: se ha construido una matriz de correlación entre todas las variables y se ha usado el método VIF (variance inflaction factor) de statsmodel para ver el valor de colinealidad de cada variable. Se observa alta colinealidad entre edad media y plantilla. Se decide descartar la variable edad media.
* Análisis de estacionalidad: se procede a analizar la estacionalidad de las variables que requerirán ser predichas (objetivo, plantilla, casos covid y búsquedas google) para ver si pueden predecirse a través de un modelo de time series.
	* Seasonal descompose: mediante este método se observa estacionalidad en todas las variables
	* Analisis adfuller: mediante este método se observa estacionalidad en todas las variables.
	* Función ACF: mediante este método no se puede observar estacionalidad en variable casos covid.
* Feature importance: se procede a calcular la importancia que tiene cada variable independiente para predecir el valor de la variable objetivo. Para ello se usa el método de regresión de Random Forest usando los datos históricos como set de entrenamiento y se llama a la propiedad feature_importance. Se observa que las 3 variables más importantes son: Casos covid (0.41), Plantilla (0.35) y búsquedas en google (0.13).

## Grid Search and Evaluation
Se ha decidido predecir la variable objetivo mediante un modelo de predicción de series temporales. Se ha probado con el método Arima y con FbProphet. Se han definido dos funciones para hallar el modelo con los mejores parámetros y variables con los siguientes componentes:
* Iterar cada test de modelo con cada combinación posible de variables independientes.
* Autoarima en la función de Arima para encontrar los mejores parámetros en cada iteración
* Evaluar cada modelo en función del Error Cuadrático Medio en Porcentaje.
Los resultados nos dicen que los mejores modelos deben introducir los datos a futuro de las variables exógenas y de las 5 que tenemos:
* Normativa: sabemos que será 0.
* Festivos intersemanales: sabemos cuántos días tendrá cada mes gracias a visualizar el [calendario laboral de Badalona](https://www.elperiodico.com/es/economia/20211014/calendario-laboral-festivos-badalona-2022-12252155)
* Plantilla: es estacionaria y, por tanto, se predicen los valores mediante modelo de series temporales (FbProphet es el que presenta un error menor).
* Búsquedas Google: es estacionaria y, por tanto, se predicen los valores mediante modelo de series temporales (Sarimax es el que presenta un error menor).
* Casos Covid: no es estacionaria y, por tanto, no se puede predecir mediante modelos de series temporales. Se decide predecir tomando como referencia la evolución de casos covid en Sudáfrica por ser este país el primero en sufrir la ola de la variante Omicron. De todas formas, se decide crear 2 modelos: uno teniendo en cuenta la variable covid y otro sin.

## Conclusion
* Con el modelo 1 (teniendo en cuenta la variable covid) se estima que la plantilla acumulada que estará de baja laboral es 622,34. Esto podría suponer un coste de cobertura de hasta 1.564.309€. Con la partida presupuestaria destinada a este efecto si se decidiese cubrir la totalidad de las bajas aún sobraría 342.656€.
* Con el modelo 2 (sin tener en cuenta la variable covid) se estima que la plantilla acumulada que estará de baja laboral es 674,65. Esto podría suponer un coste de cobertura de hasta 1.695.776€. Con la partida presupuestaria destinada a este efecto si se decidiese cubrir la totalidad de las bajas aún sobraría 193.189€.
* La diferencia de un modelo al otro se debe a que el modelo con la variable covid sabe que los casos covid afectan a las bajas laborales y, como los datos a futuro le dicen que los casos bajarán y se mantendrán al mínimo, predice que las bajas laborales serán inferiores y con ligera variabilidad. En cambio, en el otro modelo no se da esa información y el modelo deduce que la evolución de las bajas será similar a la de los últimos meses. Es decir, subidas y bajadas abruptas (siguiendo por eso la tendencia estacionaria).

## Future Work
* Sería conveniente replicar los pasos de este proyecto a todas las categorías profesionales del hospital y habría automatizar el entrenamiento del modelo y la predicción de los datos. Al acercarse a final de año el espacio de predicción será menor y los resultados se cree que serán mejores.
* Sería interesante incorporar una fórmula que puede predecir la evolución de los casos covid a futuro.
* Se podría intentar añadir otra variable con información del clima mensual para mejorar los resultados de los modelos.

## Organization
* Todos los recursos se encuentran en la rama “main”.
* El código Python con todos los pasos seguidos se encuentra en la carpeta raíz del directorio en los siguientes notebooks de jupyter:
	* Proyecto_Final_EDA.ipynb
	* Proyecto_Final_Model_Enfermeria.ipynb
	* Proyecto_Final_Predicciones-exogenas_con_covid.ipynb
	* Proyecto_Final_Predicciones-exogenas_sin_covid.ipynb
* Carpeta data/ => datasets generales
* Carpeta models/ => modelos exportados
* Carpeta output/ => datasets específicos para cada categoría profesional.

## Links

[Repository](https://github.com/cmromero/PR-Final-Sick_leave_prediction)  
[Slides](https://docs.google.com/presentation/d/1LLYgpm1lkzQl7cDWJNk5t0_gg0AmX-AkXVuehJzQJeU/edit?usp=sharing) 
