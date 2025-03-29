# Señales electromiográficas EMG
## Introducción
A continuación encontrará el código pertinenete a un programa que gráfica una señal EMG filtrada mediante el módulo de elctromiografía, así mismo se hace uso del aventanamiento mediante la técnica Hamming con su respectivo análisis espectral a partir del método Welch que es una variante mejorada de la FFT, lo anterior será realizado de manera progresiva hasta alcanzar la fátiga muscular con la finalidad de ideintificar el cambio del espectro y evaluar el comportamiento de las frecuencias. Basado en lo anterior, se toma una señal EMG mediante el módulo AD8832 conectándolo al antebrazo mediante electrodos, posteriormente esta señal es trasmitida mediante el DAQ al computador para su posterior análisis ya descrito.

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
### Adquisición de datos
1. Este código permite la adquisición, verificación, almacenamiento y visualización de datos desde un dispositivo DAQ NI. Primero, configura los parámetros de medición, como el canal de entrada, la frecuencia de muestreo y la duración de la adquisición. Luego, captura la señal en segmentos de un segundo, almacenando los datos en un arreglo de NumPy. Posteriormente, verifica la calidad de la señal para detectar posibles errores. Si los datos son válidos, los guarda en un archivo .npy para facilitar su análisis. Finalmente, grafica la señal adquirida, ajustando la cantidad de puntos si la muestra es muy grande, con el objetivo de proporcionar una representación clara de la variación del voltaje en función del tiempo. 
```python
import nidaqmx
import numpy as np
import time
import matplotlib.pyplot as plt

DEVICE_NAME = "Dev1"    
CHANNEL = "ai1"         
SAMPLE_RATE = 1000    
DURATION = 120           
FILENAME_NPY = "datos_señal.npy"  

def adquirir_datos():
    num_samples = SAMPLE_RATE * DURATION  # Número total de muestras a adquirir
    data = np.zeros(num_samples)  # Prealocar espacio para eficiencia
    
    with nidaqmx.Task() as task:
        task.ai_channels.add_ai_voltage_chan(f"{DEVICE_NAME}/{CHANNEL}")
        task.timing.cfg_samp_clk_timing(SAMPLE_RATE, samps_per_chan=num_samples)

        print(f"Adquiriendo datos durante {DURATION // 60} minutos...")
        start_time = time.time()

        for i in range(DURATION):
            chunk = task.read(number_of_samples_per_channel=SAMPLE_RATE)  
            data[i * SAMPLE_RATE : (i + 1) * SAMPLE_RATE] = chunk  # Guardar en la matriz

            elapsed = time.time() - start_time
            print(f" Progreso: {i+1}/{DURATION} segundos ({elapsed:.1f} s transcurridos)", end="\r")

    print("Adquisición completada.")
    return data

def verificar_datos(data):
    if np.all(data == 0):
        print("Advertencia: No se detecta señal, verifica la conexión.")
        return False
    if np.std(data) < 0.001:
        print("Advertencia: La señal parece demasiado estable, ¿es correcto?")
        return False
    print(" Señal detectada correctamente.")
    return True

def guardar_datos_npy(data, filename):
    np.save(filename, data)
    print(f"Datos guardados en {filename}")

def graficar_datos(data):
    tiempo = np.arange(0, len(data)) / SAMPLE_RATE  # Crear vector de tiempo

    plt.figure(figsize=(12, 5))
    
    if len(data) > 50000:  # Si hay más de 50,000 puntos, graficar una muestra representativa
        step = len(data) // 50000
        plt.plot(tiempo[::step], data[::step], label="Señal adquirida (muestra reducida)", color='b', linewidth=0.5)
    else:
        plt.plot(tiempo, data, label="Señal adquirida", color='b', linewidth=0.5)

    plt.xlabel("Tiempo (s)")
    plt.ylabel("Voltaje (V)")
    plt.title("Señal Adquirida desde DAQ (5 minutos)")
    plt.legend()
    plt.grid(True)
    
    plt.show()

datos = adquirir_datos()
if verificar_datos(datos):
    guardar_datos_npy(datos, FILENAME_NPY)
    graficar_datos(datos)
```
2. Una vez obtenidos los datos en un nuevo programa se importan las librerías numpy para el manejo de arreglos numéricos, matplotlib.pyplot para graficar los datos, scipy.stats ttest_ rel para obtener el valor de t, scipy.signal welch para calcular el espectro de potencia, y scipy.signal.windows.hamming  para aplicar una ventana de Hamming de acuerdo con el análisis espectral obtenido. Posteriormente se define la frecuencia de muestreo a utilizar en la señal y se carga el archivo de datos que contiene la señal EMG.
```python
import numpy as np
import matplotlib.pyplot as plt
from scipy.stats import ttest_rel, norm
from scipy.signal import welch
from scipy.signal.windows import hamming

SAMPLE_RATE = 1000 
FILENAME_NPY = "datos_señal.npy"

datos = np.load(r"C:\Users\Camila Martinez\Desktop\datos_señal.npy")
```
3. Se grafica la señal EMG definiendo la función graficar_datos(data, fs), la cual calcula el eje de tiempo, grafica la señal y la muestra. Llamando a la función para visualizar la gráfica obtenida.
```python
def graficar_datos(data, fs):
    tiempo = np.arange(len(data)) / fs
    plt.figure(figsize=(12, 5))
    plt.plot(tiempo, data, label="Señal EMG", color='b', linewidth=0.5)
    plt.xlabel("Tiempo (s)")
    plt.ylabel("Voltaje (V)")
    plt.title("Señal EMG Adquirida (Sin Filtrar)")
    plt.legend()
    plt.grid(True)
    plt.show()

graficar_datos(datos, SAMPLE_RATE)
```
4. Se crea una función la cual calcula la frecuencia mediana dividiendo la potencia espectral en dos partes iguales, para analizar mejor los datos obtenidos
```python
def calcular_frecuencia_mediana(frecuencia, espectro):
    espectro_acumulado = np.cumsum(espectro)
    total_potencia = espectro_acumulado[-1]
    indice_mediana = np.where(espectro_acumulado >= total_potencia / 2)[0][0]
    return frecuencia[indice_mediana]
```
### Análisis de la fatiga muscular
5. Se analiza la evolución del espectro de la señal EMG para detectar fatiga muscular. Primero, la señal se divide en dos mitades y a cada una se le aplica el método de Welch que permite observar como cambia la energía en diferentes frecuencias, utilizando una ventana de Hamming de 256 muestras, para estimar la densidad espectral de potencia. Luego, se compara el espectro de ambas mitades mediante una prueba t, evaluando si hay una diferencia estadísticamente significativa en la distribución espectral. Un p-valor menor a 0.05 indica que la segunda mitad de la señal tiene un desplazamiento hacia frecuencias más bajas.
```python
def analizar_fatiga(data, fs):
    ventana = hamming(256)  
    frecuencias, espectro = welch(data, fs=fs, window=ventana, nperseg=256)

    ventana_inicial = data[:len(data) // 2]
    ventana_final = data[len(data) // 2:]

    f_ini, e_ini = welch(ventana_inicial, fs=fs, window=ventana, nperseg=256)
    f_fin, e_fin = welch(ventana_final, fs=fs, window=ventana, nperseg=256)

    if np.any(np.isnan(e_ini)) or np.any(np.isnan(e_fin)):
        print(" Advertencia: Hay valores NaN en los espectros.")

    min_len = min(len(e_ini), len(e_fin))
    e_ini, e_fin = e_ini[:min_len], e_fin[:min_len]

    t_stat, p_val = ttest_rel(e_ini, e_fin)

    mediana_ini = calcular_frecuencia_mediana(f_ini, e_ini)
    mediana_fin = calcular_frecuencia_mediana(f_fin, e_fin)

    if p_val < 0.05:
        hipotesis = "Se rechaza H0: hay diferencia significativa entre las medianas."
    else:
        hipotesis = "No se rechaza H0: no hay diferencia significativa entre las medianas."
```
6. Se genera un gráfico que  muestra el espectro de potencia de la señal, resaltando con líneas verticales la frecuencia mediana inicial y final para visualizar cómo cambia la distribución de energía en el dominio de la frecuencia. 
 ```python
    plt.figure(figsize=(12, 6))
    plt.fill_between(frecuencias, espectro, color="r", alpha=0.6, label="Espectro de Potencia")
    plt.axvline(mediana_ini, color="b", linestyle="--", label="Mediana Inicial")
    plt.axvline(mediana_fin, color="g", linestyle="--", label="Mediana Final")
    plt.xlabel("Frecuencia (Hz)")
    plt.ylabel("Densidad Espectral de Potencia (V²/Hz)")
    plt.title("Análisis Espectral de la Señal EMG (Sin Filtrar)")
    plt.legend()
    plt.grid()
    plt.ylim(-max(espectro) * 0.1, max(espectro) * 1.1)
    plt.show()
```
7.  Después encotramos un segundo gráfico que compara directamente los espectros de la primera y la segunda mitad de la señal, utilizando diferentes colores y estilos de línea para evidenciar posibles desplazamientos en la energía espectral. Para así ejecutar todo el análisis de la señal cargada.
```python
    plt.figure(figsize=(12, 6))
    plt.plot(f_ini, e_ini, label="Espectro Inicial", color="b")
    plt.plot(f_fin, e_fin, label="Espectro Final", color="g", linestyle="dashed")
    plt.xlabel("Frecuencia (Hz)")
    plt.ylabel("Densidad Espectral de Potencia (V²/Hz)")
    plt.title("Comparación de Espectro Inicial y Final")
    plt.legend()
    plt.grid()
    plt.show()

analizar_fatiga(datos, SAMPLE_RATE)
```
8. Por último obtenemos el gráfico de la distribución t con sus respectivas zonas de rechazo.
```python
    x = np.linspace(-4, 4, 1000)
    y = norm.pdf(x, 0, 1)  # Distribución normal estándar
    t_critico = 1.96  # Valor crítico para un nivel de confianza del 95%
    
    plt.figure(figsize=(10, 5))
    plt.plot(x, y, color="blue", label="Distribución Normal")
    if not np.isnan(t_stat):
        plt.axvline(t_stat, color="r", linestyle="--", label=f"t = {t_stat:.2f}")
    plt.axvline(-t_critico, color="black", linestyle="--", label=f"Región de Rechazo: ±{t_critico}")
    plt.axvline(t_critico, color="black", linestyle="--")
    plt.fill_between(x, y, where=(x <= -t_critico), color="red", alpha=0.5)
    plt.fill_between(x, y, where=(x >= t_critico), color="red", alpha=0.5)
    plt.xlabel("Valor de t")
    plt.ylabel("Densidad de Probabilidad")
    plt.title("Distribución t con Regiones de Rechazo")
    plt.legend(loc='upper left')  # Mover la leyenda a la izquierda
    plt.grid()
    plt.show()
```
## Requerimientos
1. Python 3.12, mathlab, NI
2. Librerias numpy, matplotlib, scipy
3. Sensor de electromiografía
4. DAQ

## Para referenciar
Señales electromiográficas EMG by Camila Martínez Y Paula Vega published 28/03/25

## Referencias
