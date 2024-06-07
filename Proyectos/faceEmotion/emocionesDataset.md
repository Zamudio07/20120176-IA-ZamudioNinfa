```
import cv2 as cv
import numpy as np 
import os 
```
```
dataSet = 'C:\\Users\\zamud\\Desktop\\emociones\\'
faces = os.listdir(dataSet)
print(faces)

labels = []
facesData = []
label = 0 
```

```
for face in faces:
    facePath = os.path.join(dataSet, face)
    for faceName in os.listdir(facePath):
        labels.append(label)
        facesData.append(cv.imread(os.path.join(facePath, faceName), 0))
    label += 1

print(np.count_nonzero(np.array(labels) == 0)) 
```
```
LBPHFace = cv.face.LBPHFaceRecognizer_create()
LBPHFace.train(facesData, np.array(labels))
LBPHFace.write('emociones_lbph.xml')
```