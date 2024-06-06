import cv2 as cv
import numpy as np 
import os 

# Define el directorio del dataset
dataSet = 'C:\\Users\\zamud\\Desktop\\proyecto1_emociones\\'
faces = os.listdir(dataSet)
print(faces)

labels = []
facesData = []
label = 0 

# Recorrer cada directorio de emociones (enojado, feliz, etc.)
for face in faces:
    facePath = os.path.join(dataSet, face)
    for faceName in os.listdir(facePath):
        labels.append(label)
        facesData.append(cv.imread(os.path.join(facePath, faceName), 0))
    label += 1

print(np.count_nonzero(np.array(labels) == 0)) 

# Crear el reconocedor de rostros LBPH
LBPHFace = cv.face.LBPHFaceRecognizer_create()
# Entrenar el reconocedor
LBPHFace.train(facesData, np.array(labels))
# Guardar el modelo entrenado
LBPHFace.write('emociones_lbph.xml')


