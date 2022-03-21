<img src="https://bit.ly/2VnXWr2" alt="Ironhack Logo" width="100"/>

# Predicción de bajas laborales
*Carlos Romero*

*Data0921, Barcelona, 2022-03-21*

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
- [Workflow](#workflow)
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
*Include this section only if you chose to include ML in your project.*
* Describe how you trained your model, the results you obtained, and how you evaluated those results.

## Conclusion
* Summarize your results. What do they mean?
* What can you say about your hypotheses?
* Interpret your findings in terms of the questions you try to answer.

## Future Work
Address any questions you were unable to answer, or any next steps or future extensions to your project.

## Workflow
Outline the workflow you used in your project. What were the steps?
How did you test the accuracy of your analysis and/or machine learning algorithm?

## Organization
How did you organize your work? Did you use any tools like a trello or kanban board?

What does your repository look like? Explain your folder and file structure.

## Links
Include links to your repository, slides and trello/kanban board. Feel free to include any other links associated with your project.


[Repository](https://github.com/cmromero/PR-Final-Sick_leave_prediction)  
[Slides](https://docs.google.com/presentation/d/1LLYgpm1lkzQl7cDWJNk5t0_gg0AmX-AkXVuehJzQJeU/edit?usp=sharing) 
