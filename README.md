# Señales electromiográficas EMG
## Introducción
A continuación encontrará el código pertinenete a un programa que gráfica una señal EMG filtrada mediante el módulo de elctromiografía, así mismo se hace uso del aventanamiento mediante la técnica Hamming con su respectivo análisis espectral a partir de la FFT, lo anterior será realizado de manera progresiva hasta alcanzar la fátiga musuclar con la finalidad de ideintificar el cambio del espectro y evaluar el comportamiento de las frecuencias. Basado en lo anterior, se toma una señal EMG mediante el módulo AD8832 concetandolo al antebrazo mediante electrodos, posteriormente esta señal es trasmitida mediante el DAQ al computador para su posterior análisis ya descrito.

## Resultados 
A partir de la señal EMG capturada y el código realizado posteriormente en pyhton, se obtuvo primeramente la siguiente señal que es filtrada directamente por el sensor EMG gracias a su configuración:

![EMG1](https://github.com/user-attachments/assets/c7e8ec87-996f-48bc-87f7-f382f4b49aa7)

En esta se logra observar las contracciones musculares producidas, mostrando los picos (contracción) y posteriormente su relajación; asimismo se evidencia que al final hubo una mayor frecuencia en las contracciones musculares y con mayor intensidad dado que llegan en su mayoria a los siete voltios dado que, al llegar a la fatiga muscular los músculos se activan con mayor frecuencia para compensar el esfuerzo.

Posteriormente se obtuvo el espectro de la señal 

<img src="https://github.com/user-attachments/assets/01fb85d2-6af8-44b3-bbde-240d0db2690b" width="710">

Lo anterior, demuestra que la mayor densidad espectral se concentra en las bajas frecuencias, así mismo se observa un pico que pudo ser dado por ruidos externos como los artefactos, por otra parte se encuentra que a medida que la frecuencia aumenta la densidad espectral disminuye, lo cual es característico de las señales EMG dado que el rango de utilidad se encuentra entre los 20 a 250Hz, se percibe la presencia de una mediana espectral desplazada, lo cual indica una reducción en la frecuencia dominante a lo largo del tiempo, sugiriendo la fatiga muscular.

Así mismo, se puede observar el aventanamiento obtenido con su respectivo espectro y comparación entre el inicial (azul, antes de la fatiga) y el final (verde, después de la fatiga)

![AVENTANAMIENTO](https://github.com/user-attachments/assets/1aecb699-7706-43e9-8880-fd7b4057eb12)

Basado en lo anterior, se observa un aumento en la densidad espectral de potencia por parte del espectro final en frecuencias bajas entre los 0 - 50Hz, lo anterior es un indicador de la fátiga muscular esto dado que las fibras de contracción rápida tienden a activarse en menor nivel (estas generan los componenetes de altas frecuencias). 
Por otra parte entre los rangos de aproximadamente 50 a 200Hz tienen un comportanmiento similar los espectros, por lo anterior se evidencia que la fátiga muscular es mostrada en frecuencuas altas principalmente, igualmente para el caso de frecuencias mayores a 200Hz.

Finalmente se grafica la representación de la distribución normal estándar con media en 0 y simétrica en torno al eje vertical, la cual se usa para evaluar la significancia estadística en pruebas de hipótesis.

![DISTRI](https://github.com/user-attachments/assets/3ae69907-542b-40d6-8a92-02bdba54a93f)

De esta manera, las líneas negras discontinuas marcan los valores críticos para un nivel de confianza del 95% en una prueba bilateral, el 5% de probabilidad  se divide en los extremos, de manera que quedan 2.5% a la izquierda a la derecha. De esta forma la línea roja discontinua se ubica en la parte izquierda de la curva y se encuentrafuera del intervalo de aceptación (-1.96, 1.96), de manera que el resultado es poco probable. 

## Instrucciones


## Requerimientos
1. Python 3.12, mathlab, NI
2. Librerias numpy, matplotlib, scipy
3. Sensor de electromiografía
4. DAQ

## Para referenciar
Señales electromiográficas EMG by Camila Martínez Y Paula Vega published 11/02/25

## Referencias
