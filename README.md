<img src="https://bit.ly/2VnXWr2" alt="Ironhack Logo" width="100"/>

# Predicción de bajas laborales
*Carlos Romero*

*Data0921, Barcelona, 21/03/2022*

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
- Para los casos covid se ha usado un dataset disponible en el web de dades obertes de Catalunya y se ha usado la API de Socrata para automatizar la actualización de datos.
- Para las búsquedas en google de términos relacionados con la gripe se ha usado la API no oficial de Google Trends llamada pytrends. 
- Para estimar los datos a futuro de casos covid se han importado los datos publicados en https://x-y.es/covid19/sudafrica.pais

## Cleaning
Describe your full process of data wrangling and cleaning. Document why you chose to fill missing values, extract outliers, or create the variables you did as well as your reasoning behind the process.

## Analysis
* Overview the general steps you went through to analyze your data in order to test your hypothesis.
* Document each step of your data exploration and analysis.
* Include charts to demonstrate the effect of your work.
* If you used Machine Learning in your final project, describe your feature selection process.

## Model Training and Evaluation
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
