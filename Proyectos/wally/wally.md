```
import numpy as np
import cv2 as cv
import os
image = cv.imread(r'C:\\Users\\zamud\\Desktop\\wally\\wally3.png')

gray = cv.cvtColor(image, cv.COLOR_BGR2GRAY)

wally = cv.CascadeClassifier(r'C:\\Users\\zamud\\Desktop\\wally\\wallycascade.xml')

waldo_detections = wally.detectMultiScale(gray, scaleFactor=1.3, minNeighbors=3, minSize=(5,5))

for (x, y, w, h) in waldo_detections:
    cv.rectangle(image, (x, y), (x+w, y+h), (0, 255, 0), 2)
    cv.putText(image, 'Wally', (x, y-10), cv.FONT_HERSHEY_SIMPLEX, 0.9, (0, 255, 0), 2)


cv.imshow('¿Donde esta Wally?', image)
cv.waitKey(0)
cv.destroyAllWindows()
```