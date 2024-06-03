//JUEGO 15/04
demo1.js
En el juego (2, 6, 6, 2) 2 entradas, 6 neuronas por capas ocultas los dos 6 y por ultimo el otro 2 es capa de salida. 
si fuera (2, 6, 6, 4, 10, 2): 2 entradas con 4 capas ocultas, cada una con un valor diferente (6, 6, 4, 10) y 2 capas de salidas 

Todo esto se conecta como si fueran neuronas. 

nnNetwork.activate, esto sirve para utilizar el entrenamiento que se estuvo haciendo.

Funcion simboide 
Por cada salida se tienen dos opciones se tiene un 1 o un 0. 
(4,6,6,6,2): se puede tener de esta manera, porque solo regresa dos cosas 1 o 0 y con dos salidas se puede. 

1. generar dataset 

output: etiquetas, clasifica la informacion 

Actividad 18/04 
cargar el codigo del juego y cargar los datos en una grafica. 

Para el lunes documentar todo lo que hace el juego, en que punto se pone el modelo, se genera el data set, un diagrama de flujo, etc. 
Donde estan las funciones de aprendizaje y como llamo a la red neuronal.

22/04

Hacer un modelado de las entradas y de las salidas. Velocidad de la gravedad, esto influye mucho porque se ve afectado 
entradas, salidas, en que partes del codigo se deberian hacer las modificaciones necesarias para que el juego funcione

- Velocidad del personaje (vx, vy): La velocidad a la que se mueve el personaje en dirección horizontal y vertical.

- Aceleración del personaje (ax, ay): La aceleración del personaje en dirección horizontal y vertical.

- Velocidad de la bala (vx, vy): La velocidad a la que se mueve la bala en dirección horizontal y vertical.

- Aceleración de la bala (ax, ay): La aceleración de la bala en dirección horizontal y vertical.

- Distancia entre el personaje y la bala: La distancia euclidiana entre la posición del personaje y la posición de la bala en cada paso de tiempo.

