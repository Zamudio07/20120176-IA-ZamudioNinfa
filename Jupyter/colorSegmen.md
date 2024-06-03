# COLOR SEGMENTATION IN VIDEO


```python
De igual forma que con las imagenes, se hace una mascara con el rango de colores desaeados.
```


```python
import cv2 as cv

cap = cv.VideoCapture(0)
while(True):
    res , img = cap.read()
    img2=cv.cvtColor(img, cv.COLOR_BGR2HSV)
    vb=(120, 60,60)
    va=(170, 255,255)
    mask=cv.inRange(img2, vb,va)
    res=cv.bitwise_and(img, img, mask=mask)
    cv.imshow('captura', res)
    if cv.waitKey(1) &0xFF == ord('s'):
        break
       
cap.release()
cv.destroyAllWindows()
```


```python
import cv2 as cv


cap = cv.VideoCapture(0)
while(True):
    res , img = cap.read()
    img2=cv.cvtColor(img, cv.COLOR_BGR2HSV)
    ubb=(110,130,20)
    uba=(150,200,150)
    mask=cv.inRange(img2, ubb,uba)
    res=cv.bitwise_and(img, img, mask=mask)
    cv.imshow('Segmentado', res)
    cv.imshow('Real', img)
    ret, frame = cap.read()
    height, width, _ = frame.shape
    if cv.waitKey(1) &0xFF == ord('s'):
        break
       
cap.release()
cv.destroyAllWindows()
```

Podemos localizar la coordenada donde está el objeto. Lo ideal es analizar la máscara (Que es donde se mueve el objeto segmentado).


```python
import cv2 as cv
cap = cv.VideoCapture(0)
while(True):
    res , img = cap.read()
    img2=cv.cvtColor(img, cv.COLOR_BGR2HSV)
    ubb=(110,130,20)
    uba=(150,200,150)
    mask=cv.inRange(img2, ubb,uba)
    res=cv.bitwise_and(img, img, mask=mask)
    cv.imshow('Segmentado', res)
    cv.imshow('Real', img)
    ret, frame = cap.read()
    height, width, _ = frame.shape
    if cv.waitKey(1) &0xFF == ord('s'):
        break
       
cap.release()
cv.destroyAllWindows()
```


```python
import cv2 as cv
import numpy as np

# Definimos la función para obtener los pixeles de la máscara
def obtener_pixeles_mascara(mask):
  # Encontramos los índices de los pixeles que coinciden con la máscara
  y, x = np.where(mask == 255)

  # Convertimos las coordenadas a enteros
  x = x.astype(int)
  y = y.astype(int)

  # Devolvemos las coordenadas de los pixeles
  return x, y

# Capturamos la cámara web
cap = cv.VideoCapture(0)

while True:
  # Leemos un fotograma
  ret, img = cap.read()

  # Convertimos el fotograma a HSV
  img2 = cv.cvtColor(img, cv.COLOR_BGR2HSV)

  # Definimos el rango de color para la máscara
  ubb = (110, 130, 20)
  uba = (150, 200, 150)

  # Aplicamos la máscara al fotograma
  mask = cv.inRange(img2, ubb, uba)

  # Obtenemos los pixeles que cumplen con la máscara
  pixeles_x, pixeles_y = obtener_pixeles_mascara(mask)

  # Mostramos el fotograma original
  cv.imshow('Real', img)

  # Mostramos la máscara
  cv.imshow('Segmentado', mask)

  # Dibujamos un círculo rojo en cada pixel con la máscara
  for i in range(len(pixeles_x)):
    cv.circle(img, (pixeles_x[i], pixeles_y[i]), 5, (0, 0, 255), -1)

  # Mostramos el fotograma con los pixeles marcados
  cv.imshow('Localización por pixelaje', img)

  # Salimos del bucle si se presiona la tecla "s"
  if cv.waitKey(1) & 0xFF == ord('s'):
    break
cap.release()
cv.destroyAllWindows()

```


```python

```