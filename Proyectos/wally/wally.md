import numpy as np
import cv2 as cv
import os
image = cv.imread(r'E:\\IA\\Proyectos\\proyecto2_wally\\wally3.png')

gray = cv.cvtColor(image, cv.COLOR_BGR2GRAY)

wally = cv.CascadeClassifier(r'E:\\IA\\Proyectos\\proyecto2_wally\\wallycascade.xml')

waldo_detections = wally.detectMultiScale(gray, scaleFactor=1.3, minNeighbors=3, minSize=(5,5))

for (x, y, w, h) in waldo_detections:
    cv.rectangle(image, (x, y), (x+w, y+h), (0, 255, 0), 2)
    cv.putText(image, 'Wally', (x, y-10), cv.FONT_HERSHEY_SIMPLEX, 0.9, (0, 255, 0), 2)


cv.imshow('¿Donde esta Wally?', image)
cv.waitKey(0)
cv.destroyAllWindows()