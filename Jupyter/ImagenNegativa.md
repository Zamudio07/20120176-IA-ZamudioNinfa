```
import cv2 as cv
```

```
img = cv.imread('C:\\Users\\zamud\\pictures\\imagen.jpg', 0)
print(img.shape)
w, h = img.shape
cv.imshow('Ejemplo', img)
for i in range(w):
    for j in range(h):
        #img[i,j] = 255-img[i,j]
        if(img[i,j]>150):
            img[i,j]=255
        else:
            img[i,j]=0
cv.imshow('Ejemplo2', img)            
cv.waitKey(0)
cv.destroyAllWindows()
    (1000, 1000)
```
    
```
cv.imshow('Ejemplo', img)
cv.waitKey(0)
cv.destroyAllWindows()
```