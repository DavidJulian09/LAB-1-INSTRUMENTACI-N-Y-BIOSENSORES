# LAB-1 MONITOREO DEL PATRÓN Y FRECUENCIA RESPIRATORIA 

## PARTE A: Adquisición de datos

### a) Proceso Respiratorio y variables relacionadas

El proceso de respiración es el mecanismo por el cual realiza el intercambio de gases con el ambiente, con el objetivo de mantener el ambiente metabólico de nuestras células. Es un proceso que posee dos etapas, la inspiración donde el diafrágma se comprime y ocurre la entrada de aire (O2), y la inhalación el diafragma se relaja y se da la salida de gases como CO2, estos gases fueron el pilar de estudio en este laboraturio, pues mediante la toma de datos del movimiento de dichos gases se pudo determinar valores como la frecuencia respiratoria.

### b) Justificación Sensor Seleccionado

El sensor empleado para la presente práctica corresponde a un sensor de gases basado en dioxido de carbono (CO2) MQ-135, cuya capacidad de detectar las variaciones en la concentración de gases nos permitió la medición indirecta del ciclo respiratorio y en consecuencia, de su frecuencia respiratoria. Adicionalmente, su simplicidad permite la salida analoga a un microcotrolador (ESP 32) para realizar la correspondiente conversión analogo-digital; junto con su capacidad de ajustar su nivel de sensibilidad de forma manual, lo convirtieron en una opción simple y óptima.

A pesar de lo anterior, este sensor posee ciertas limitaciones, entre ellas el hecho de que no diferencia gases como el NH3, alcoholes, humo y CO2, siendo este último el que mas interés guarda para la medición de la respiración, aunque siendo el objetivo de la práctica la medición de la frecuencia respiratoria, el flujo de gases fue la variable optima para cumplir dicho propósito; por otro lado, depende de ciertos factores externos, como la humedad y el tiempo de calentamiento del sensor, estas variables alteran la presición del mismo a la hora de la toma de la señal.

### c) Sistema Digitalización Señal Análoga

Una vez determinado el sensor a usar, se prosiguió a hacer la adquisición de la señal, en donde la conversión análogo-digital se llevo por medio de un microcontrolador ESP32, a continuación se mestra el montaje realizado:

<img width="1600" height="1201" alt="image" src="https://github.com/user-attachments/assets/1c8eccc8-b240-43bd-b223-4dc13c750730" />

_(Fig 1. Conección sensor MQ-135 y ESP32)_

Como se observa el circuito carece de filtros análogos ya que la filtración de la señal se hará de forma digital (Parte B). Una vez realizada la toma de la señal por medio del microcontrolador, se prosiguió con la comunicación serial entre arduino y Matlab donde se presentaron los resultados crudos de la señal antes de su posterior procesamiento (filtrado y métricas) por medio de gráficas, dichas gráficas se muestran a continuación, siendo la primera correspondiente a una respiración en resposo y la segunda a una respiración mientras se verbaliza.

<img width="766" height="630" alt="image" src="https://github.com/user-attachments/assets/f6bfdd38-02c9-4eb5-abfb-aafed2b5374f" />

_(Fig 2. Señal cruda respiración en reposo)_

<img width="766" height="630" alt="image" src="https://github.com/user-attachments/assets/9cdedcab-dfb8-4ea4-a5bc-ef118877c688" />

_(Fig 3. Señal cruda respiración durante verbalización)_

El análisis de los resultados fue realizado en la Parte C, en donde se comparan las diferencias entre las señales.

## PARTE B: Programación en Matlab

### a) Captura Temporizada de los Datos

En primera instancia, se busco un modo en el que se tomara una ventana de tiempo 'x' deseada por el usuario, con la finalidad de tomar una cantidad específica de datos deseados. El fragmento de código encargado de esto fue:

     respuesta = inputdlg('Ingrese el tiempo de adquisición (segundos):', ...
                          'Tiempo de captura', [1 40], {'60'});
    T = str2double(respuesta{1});

    %Parámetros
    Fs = 25;       %frecuencia de muestreo
    N  = Fs * T;
    senal = zeros(1, N);
    tiempo = (0:N-1)/Fs;

    %Adquisición
    for k = 1:N
        dato = readline(s);
        senal(k) = str2double(dato);
        set(h, 'YData', senal);
        drawnow;
    end

La función "inputdlg" permite la apertura de una ventana que solicita el tiempo "T", este tiempo junto con la frecuencia de muestreo (Fs), determina el número de muestras que tomará el código por medio del bucle for presentado; a su vez mientras se realiza la toma de los datos se mostrará la gráfica de los datos en tiempo real.

### b) Filtros Empleados y Señal

Una vez realizada la toma de los datos, se prosiguió con su filtrado, en donde se emplearon dos; el primero fue un filtro de mediana que se encargaba de eliminar picos erráticos (ruido), mostrando lo cambios respiratorios de una forma más suave, todo lo anterior tomando una ventana de muestras vecinas y reemplazando su valor por la mediana.

    %Filtro mediana
    senal_med = medfilt1(senal, 5);

Adicionalmente posee un filtro pasa-bajos encargado de eliminar los valores de bajas frecuencias, propios de gases del ambiente, así como del ambiente, que no corresponden a la respiración de la persona que emplea el sensor.

    %Filtro pasa bajos
    [b,a] = butter(2, fc/(Fs/2), 'low');
    senal_filtrada = filtfilt(b, a, senal_med);

### c) Extracción de Frecuencia Respiratoria 

Una vez filtrada la señal se realizó la lectura y el cálculo de la frecuencia respiratoria correspondiente. Lo primero que se realizó fue la detección de los picos de alta frecuencia por medio de la función "findpeaks", dichos picos corresponden a la detección de gases en la exhalación.

    %Detección de picos
    [picos, locs] = findpeaks(senal_f, Fs, ...
        "MinPeakDistance", 1.5, ...
        "MinPeakProminence", 50);

Para que el código determinara que picos fueran considerados validos y cuales no se uso "MinPeakDistance" y "MinPeakProminence", el primero se encarga de establecer una distancia mínima entre picos consecutivos, en nuestro caso usamos 1.5 segundos, con el propósito de no considerar picos oscilaciones de un mismo ciclo de exhalación; MinPeakProminence se encarga de establecer como pico los que poseen una diferencia de amplitud con el valle de su entorno, de ese modo el ruido no seria considerado como una respiración.

Un vez teniendo la cantidad picos correspondientes a las exhalciones y el tiempo de la toma (T), se puede realizar el cálculo de las respiraciones por minuto a través de la fórmula mostrada en el código

    %Frecuencia respiratoria
    respiraciones = length(picos);
    frecuencia_resp = (respiraciones / T) * 60;
    fprintf("Frecuencia respiratoria: %.2f respiraciones/min\n", frecuencia_resp);

## PARTE C:

### a) Análisis de Resultados 

Una vez terminado con la programción en Matlab se obtuvieron los siguientes resultados finales:

<img width="766" height="631" alt="image" src="https://github.com/user-attachments/assets/e8c634b9-074e-46db-9a9a-11136bbef6ff" />

Frecuencia respiratoria: 20.00 respiraciones/min

_(Fig 4. Señal en Reposo filtrada junto con frecuencia respiratoria)_

<img width="766" height="631" alt="image" src="https://github.com/user-attachments/assets/2f9899dc-10b4-4d7e-9da7-67b070d48e7f" />

Frecuencia respiratoria: 10.00 respiraciones/min

_(Fig 5. Señal en Verbalización filtrada junto con frecuencia respiratoria)_

Conforme a los resultados mostrados lo primero a destacar es la diferencia en las frecuencias en relación a si se está en reposo o verbalizando, durante el reposo las frecuencia resiratoria es regular y estable, en nuestro caso correspondio a 20rpm; en cambio mientras se habla la frecuencia disminuye (10rpm) y se vuelve irregular, esto se debe a las prolongaciones que hacemos mientras sostenemos un diálogo. Adicionalmente la amplitud también cambia, en reposo se presentaron picos con mayor amplitud, que contrasta con los de menor amplitud mientras se habla. Estos cambios en los patrones se discutiran más a fondo adelante.

<img width="757" height="630" alt="image" src="https://github.com/user-attachments/assets/4a7ae4f7-dbc7-4a4d-94aa-0ccfe893d04a" />

_(Fig 6. Espectro de frecuencias respiración en reposo)_

<img width="757" height="630" alt="image" src="https://github.com/user-attachments/assets/803c302b-1c09-4749-a185-bcd63fc2b9c1" />

_(Fig 7. Espectro de frecuencias respiración en verbalización)_

Finalmente se hace la comparación de los espectros de frecuencia, donde se observa que en reposo las frecuencias se agrupan a valores más pequeños, en oposición a esto, mientras se habla las frecuencias respiratorias están más distribuidas a lo largo de todo el espectro, este se debe a que en el habla, la respiración al ser más irregular permite que más frecuencias hagan parte de su proceso.

Realizando un breve análisis del alcance de nuestro sistema para detectar patologías, destacaría su capacidad de detectar alteraciones en la frecuencia respiratoria como apneas, ya que se podria programar para detectar y alertar de espacios donde hay ausencia de picos; adicionalmente se podría emplear para la detección de patrones irregulares en la respiración para enfermedades de trastornos en las vias aéreas; sin contar que por medio del estudio de la amplitud de los gases liberados se podría correlacionar con hipoventilación o hiperventilación.

Por otro lado las limitaciones de este sistema incluye el hecho de que no es capás de diferenciar los gases exhalados ni sus concentraciones, sin mencionar que no es posible la medición de la presión torácica en la inspiración o exhalación, de mismo modo que no se podrian medir valores como el flujo o el volumen de cada fase de la respiración.

### b) Discusión

**¿Son los patrones respiratorios y frecuencias respiratorias iguales o diferentes en cada caso? ¿A qué se debe esto?**

Son completamente distintas, su diferencia radica en el tipo de interacción entre el tipo de respiración y el sistema autónomo, en reposo la respiracion es un reflejo rítmico, por otro lado en la verbalización se deben realizar inhalaciones rápidas y se debe de alargar la exhalación para emitir sonidos, desencadenando en un cambio en la morfología de la señal; en la figura 5. se muestra este cambio en la forma de respirar donde los picos se ensanchan provocando una meseta, dicha meseta puede aumentar o disminuir dependiendo la velocidad a la que se habla y las pausas que se toman para respirar, cambiando su patrón temporal.

**¿Cuáles serían las ventajas y desventajas de emplear múltiples sensores para el monitoreo del proceso respiratorio? ¿Cuáles podrían ser las razones?**

Las principal ventaja de emplear multiples sensores seria la capacidad de medir todas aquellas variables que el sensor de gas no es capás de cubrir como el volumen o la presión, esto es un aspecto especialmente importante teniendo en cuenta que la respiración es un proceso mecánico y químico, donde la implementación de otros tipos de sensores permitirian la correlación de estas dos variables como la concentración del gas con el esfuerzo mecánico del tórax, facilitando el diagnóstico del paciente. Adicionalmente el uso de más sensores reducen el riesgo de mediciones de falsos positivos.

Por otro lado sus limitaciones consisten en la complejidad de su hardware y software, y la implementación de todos de forma armónica junto con sus correspondientes calibraciones; adicionalmente el coste de manufactura y mantenimiento aumentaría exponencialmente, junto con su consumo energético. Pese a lo anterior, el benficio obtenido es mayor que el de las limitaciones presentadas.



