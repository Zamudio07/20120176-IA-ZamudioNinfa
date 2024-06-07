```
import cv2 as cv
import numpy as np
```

```
img = cv.imread('C:\\Users\\zamud\\pictures\\imagen2.png', 0)
w,h = img.shape[:2]
img2 = np.zeros((w*2, h*2), dtype='uint8')
cv.imshow('Ejemplo', img)
cv.imshow('Ejemplo2', img2)     
cv.waitKey(0)
cv.destroyAllWindows()
```

```
img = cv.imread('C:\\Users\\zamud\\pictures\\imagen2.png', 0)
w,h = img.shape[:2]
img2 = np.zeros((w*2, h*2), dtype='uint8')
for i in range (w):
    for j in range (h):
        img2[i*2,j*2] = img[i,j]
cv.imshow('Ejemplo', img)
cv.imshow('Ejemplo2', img2)     
cv.waitKey(0)
cv.destroyAllWindows()
```

```
img = cv.imread('C:\\Users\\zamud\\pictures\\imagen2.png', 0)
w,h = img.shape[:2]
img2 = np.zeros((w*3, h*3), dtype='uint8')
for i in range (w):
    for j in range (h):
        img2[i*3,j*3] = img[i,j]
cv.imshow('Ejemplo', img)
cv.imshow('Ejemplo2', img2)     
cv.waitKey(0)
cv.destroyAllWindows()
```

```
def escala(imx, escala):
    width = int(imx.shape[1]*escala/100)
    height = int(imx.shape[0]*escala/100)
    size = (width, height)
    im = cv.resize(imx, size, interpolation = cv.INTER_AREA)
    return im

img = cv.imread('C:\\Users\\zamud\\pictures\\imagen2.png', 1)
r,g,b = cv.split(img)
img2 = escala(img, 200)
cv.imshow('Ejemplo', img)
cv.imshow('Ejemplo2', img2)  
cv.waitKey(0)
cv.destroyAllWindows()
```

```
img = cv.imread('C:\\Users\\zamud\\pictures\\imagen3.png', 1)
w,h = img.shape[:2]
r,g,b = cv.split(img)
img2 = np.zeros((w, h), dtype='uint8')
r = cv.merge([r, img2, img2])
g = cv.merge([img2, g,  img2])
b = cv.merge([img2, img2, b])
cv.imshow('red', r)
cv.imshow('green', g)
cv.imshow('blue', b)
img2 = escala(img, 200)
cv.imshow('Original', img)
cv.waitKey(0)
cv.destroyAllWindows()
```

```
def escala(imx, escala):
    width = int(imx.shape[1]*escala/100)
    height = int(imx.shape[0]*escala/100)
    size = (width, height)
    im = cv.resize(imx, size, interpolation = cv.INTER_AREA)
    return im
```

```
img = cv.imread('C:\\Users\\zamud\\pictures\\imagen3.png', 1)
w,h = img.shape[:2]
r,g,b = cv.split(img)
#img2 = np.zeros((w, h), dtype='uint8')
r1 = cv.merge([g, r, b])
g1 = cv.merge([b, r, g])
b1 = cv.merge([r, b, g])
cv.imshow('red', r1)
cv.imshow('green', g1)
cv.imshow('blue', b1)
#img2 = escala(img, 200)
cv.imshow('Original', img)
cv.waitKey(0)
cv.destroyAllWindows()
```