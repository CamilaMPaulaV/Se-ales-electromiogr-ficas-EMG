# Señales electromiográficas EMG
## Introducción
A continuación encontrará el código pertinenete a un programa que gráfica una señal EMG filtrada mediante el módulo de elctromiografía, así mismo se hace uso del aventanamiento mediante la técnica Hamming con su respectivo análisis espectral a partir de la FFT, lo anterior será realizado de manera progresiva hasta alcanzar la fátiga musuclar con la finalidad de ideintificar el cambio del espectro y evaluar el comportamiento de las frecuencias. Basado en lo anterior, se toma una señal EMG mediante el módulo AD8832 concetandolo al antebrazo mediante electrodos, posteriormente esta señal es trasmitida mediante el DAQ al computador para su posterior análisis ya descrito.

## Resultados 
A partir de la señal EMG capturada y el código realizdo posteriormente en pyhton, se obtuvo primeramente la siguiente señal que es filtrada directamente por el sensor EMG gracias a su configuración:

![EMG1](https://github.com/user-attachments/assets/c7e8ec87-996f-48bc-87f7-f382f4b49aa7)



## Instrucciones


## Requerimientos
1. Python 3.12, mathlab, NI
2. Librerias numpy, matplotlib, scipy
3. Sensor de electromiografía
4. DAQ

## Para referenciar
Señales electromiográficas EMG by Camila Martínez Y Paula Vega published 11/02/25

## Referencias
