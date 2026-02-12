# LAB-1 MONITOREO DEL PATRÓN Y FRECUENCIA RESPIRATORIA 

## PARTE A: Adquisición de datos

### a) Proceso Respiratorio y variables relacionadas

El proceso de respiración es el mecanismo por el cual realiza el intercambio de gases con el ambiente, con el objetivo de mantener el ambiente metabólico de nuestras células. Es un proceso que posee dos etapas, la inspiración donde el diafrágma se comprime y ocurre la entrada de aire (O2), y la inhalación el diafragma se relaja y se da la salida de gases como CO2, estos gases fueron el pilar de estudio en este laboraturio, pues mediante la toma de datos del movimiento de dichos gases se pudo determinar valores como la frecuencia respiratoria.

### b) Justificación Sensor Seleccionado

El sensor empleado para la presete práctica corresponde a un sensor de gases basdo en oxido metálico (SnO2) MQ-135, cuya capacidad de detectar las variaciones en la concentración de gases nos permitió la medición indirecta del ciclo respiratorio y en consecuencia, de su frecuencia respiratoria. Adicionalmente, su simplicidad permite la salida analoga a un microcotrolador (ESP 32) para realizar la correspondiente conversión analogo-digital; junto con su capacidad de ajustar su nivel de sensibilidad de forma manual, lo convirtieron en una opción simple y óptima.

A pesar de lo anterior, este sensor posee ciertas limitaciones, entre ellas el hecho de que no diferencia gases como el NH3, alcoholes, humo y CO2, siendo este último el que mas interés guarda para la medición de la respiración, aunque siendo el objetivo de la práctica la medición de la frecuencia respiratoria, el flujo de gases fue la variable optima para cumplir dicho propósito; por otro lado, depende de ciertos factores externos, como la humedad y el tiempo de calentamiento del sensor, estas variables alteran la presición del mismo a la hora de la toma de la señal.

### c) Sistema Digitalización Señal Análoga

Una vez determinado el sensor a usar, se prosiguió a hacer la adquisición de la señal, en donde la conversión análogo-digital se llevo por medio de un microcontrolador ESP32, a continuación se mestra el montaje realizado:

<img width="1600" height="1201" alt="image" src="https://github.com/user-attachments/assets/1c8eccc8-b240-43bd-b223-4dc13c750730" />

Como se observa el circuito carece de filtros análogos ya que la filtración de la señal se hará de forma digital (Parte B). Una vez realizada la toma de la señal por medio de arduino, se prosiguió con la comunicación serial entre arduino y Matlab donde se presentaron los resultados crudos de la señal antes de su posterior procesamiento (filtrado y métricas) por medio de gráficas, dichas gráficas se muestran a continuación, siendo la primera correspondiente a una respiración en resposo y la segunda a una respiración mientras se verbaliza.

<img width="766" height="630" alt="image" src="https://github.com/user-attachments/assets/f6bfdd38-02c9-4eb5-abfb-aafed2b5374f" />
(Fig 1. Señal cruda respiración en reposo)

<img width="766" height="630" alt="image" src="https://github.com/user-attachments/assets/9cdedcab-dfb8-4ea4-a5bc-ef118877c688" />
(Fig 2. Señal cruda respiración durante verbalización)

## PARTE B: Programación en Matlab

### Captura Temporizada de los Datos

### Filtros Empleados y Frecuencias de Corte

### Extracción de Frecuencia Respiratoria 


## PARTE C

### Análisis de Resultados 
(• Análisis 1: Determine semejanzas y diferencias (si las hay) entre la 
frecuencia y relación entre inhalaciones y exhalaciones de un individuo 
sano bajo condiciones de reposo y durante tareas de verbalización.
• Análisis 2: Evalúe el alcance y las posibles limitaciones de emplear el 
sistema construido durante la práctica para detectar patologías en seres 
humanos.)

### Discusión
(• Pregunta 1: ¿Son los patrones respiratorios y frecuencias respiratorias 
iguales o diferentes en cada caso? ¿A qué se debe esto?
• Pregunta 2: ¿Cuáles serían las ventajas y desventajas de emplear múltiples 
sensores para el monitoreo del proceso respiratorio? ¿Cuáles podrían ser 
las razones?)

### Conclusiones







