```
import numpy as np
import os
import re
import matplotlib.pyplot as plt
%matplotlib inline
from sklearn.model_selection import train_test_split
from sklearn.metrics import classification_report
```

```
import tensorflow as tf
from tensorflow.keras.utils import to_categorical
from keras.models import Sequential, Model
from tensorflow.keras.layers import Input
from keras.layers import Dense, Dropout, Flatten
from tensorflow.keras.layers import (
    BatchNormalization, SeparableConv2D, MaxPooling2D, Activation, Flatten, Dropout, Dense, Conv2D
)
from keras.layers import LeakyReLU
```

```
import numpy as np
import os
import re
import matplotlib.pyplot as plt
%matplotlib inline
from sklearn.model_selection import train_test_split
from sklearn.metrics import classification_report

dirname = os.path.join(os.getcwd(), r'C:\Users\zamud\Desktop\cnn\Imagenes')
imgpath = dirname + os.sep 
images = []
directories = []
dircount = []
prevRoot = ''
cant = 0

print("leyendo imagenes de ", imgpath)
for root, dirnames, filenames in os.walk(imgpath):
    for filename in filenames:
        if re.search(r"\.(jpg|jpeg|png|bmp|tiff)$", filename):
            cant = cant + 1
            filepath = os.path.join(root, filename)
            image = plt.imread(filepath)
            if len(image.shape) == 3:
                images.append(image)
            b = "Leyendo..." + str(cant)
            # print(b, end="\r")
            if prevRoot != root:
                print(root, cant)
                prevRoot = root
                directories.append(root)
                dircount.append(cant)
                cant = 0
dircount.append(cant)

dircount = dircount[1:]
dircount[0] = dircount[0] + 1
print('Directorios leidos:', len(directories))
print("Imagenes en cada directorio", dircount)
print('suma Total de imagenes en subdirs:', sum(dircount))
```

```
riesgos = []
indice = 0
for directorio in directories:
    name = directorio.split(os.sep)
    print(indice, name[len(name)-1])
    riesgos.append(name[len(name)-1])
    indice += 1
```

```
y = np.array(labels)
X = np.array(images, dtype=np.uint8) #convierto de lista a numpy

classes = np.unique(y)
nClasses = len(classes)
print('Total number of outputs : ', nClasses)
print('Output classes : ', classes)
```

```
train_X,test_X,train_Y,test_Y = train_test_split(X,y,test_size=0.2)
print('Training data shape : ', train_X.shape, train_Y.shape)
print('Testing data shape : ', test_X.shape, test_Y.shape)
```

```
plt.figure(figsize=[5,5])

plt.subplot(121)
plt.imshow(train_X[0,:,:], cmap='gray')
plt.title("Ground Truth : {}".format(train_Y[0]))

plt.subplot(122)
plt.imshow(test_X[0,:,:], cmap='gray')
plt.title("Ground Truth : {}".format(test_Y[0]))
```

```
train_X = train_X.astype('float32')
test_X = test_X.astype('float32')
train_X = train_X/255.
test_X = test_X/255.
plt.imshow(test_X[0,:,:])
```

```
# Change the labels from categorical to one-hot encoding
train_Y_one_hot = to_categorical(train_Y)
test_Y_one_hot = to_categorical(test_Y)

# Display the change for category label using one-hot encoding
print('Original label:', train_Y[0])
print('After conversion to one-hot:', train_Y_one_hot[0])
```

```
train_X,valid_X,train_label,valid_label = train_test_split(train_X, train_Y_one_hot, test_size=0.2, random_state=13)
```

```
riesgos_model = Sequential()
riesgos_model.add(Input(shape=(28, 28, 3)))  # Medida de las imagenes procesar 28x28
riesgos_model.add(Conv2D(32, kernel_size=(3, 3), activation='linear', padding='same'))
riesgos_model.add(LeakyReLU(negative_slope=0.1))
riesgos_model.add(MaxPooling2D((2, 2), padding='same'))
riesgos_model.add(Dropout(0.5))

riesgos_model.add(Flatten())
riesgos_model.add(Dense(32, activation='linear'))
riesgos_model.add(LeakyReLU(negative_slope=0.1))
riesgos_model.add(Dropout(0.5))
riesgos_model.add(Dense(nClasses, activation='softmax'))
```

```
riesgos_model.compile(
    loss=tf.keras.losses.categorical_crossentropy,
    optimizer=tf.keras.optimizers.SGD(learning_rate=INIT_LR),
    metrics=['accuracy']
)
```

```
riesgos_model.save(r"C:\\Users\\zamud\\Desktop\\cnn\\modelo\\riesgos_model.keras")
```

```
test_eval = riesgos_model.evaluate(test_X, test_Y_one_hot, verbose=1)
```

```
print('Test loss:', test_eval[0])
print('Test accuracy:', test_eval[1])
```

```
accuracy = riesgos_train.history['accuracy']
val_accuracy = riesgos_train.history['val_accuracy']
loss = riesgos_train.history['loss']
val_loss = riesgos_train.history['val_loss']
epochs = range(len(accuracy))
plt.plot(epochs, accuracy, 'bo', label='Training accuracy')
plt.plot(epochs, val_accuracy, 'b', label='Validation accuracy')
plt.title('Training and validation accuracy')
plt.legend()
plt.figure()
plt.plot(epochs, loss, 'bo', label='Training loss')
plt.plot(epochs, val_loss, 'b', label='Validation loss')
plt.title('Training and validation loss')
plt.legend()
plt.show()
```

```
predicted_classes2 = riesgos_model.predict(test_X)
```

```
predicted_classes=[]
for predicted_riesgos in predicted_classes2:
    predicted_classes.append(predicted_riesgos.tolist().index(max(predicted_riesgos)))
predicted_classes=np.array(predicted_classes)
```

```
predicted_classes.shape, test_Y.shape
```

```
correct = np.where(predicted_classes==test_Y)[0]
print("Found %d correct labels" % len(correct))
for i, correct in enumerate(correct[0:9]):
    plt.subplot(3,3,i+1)
    plt.imshow(test_X[correct].reshape(28,28,3), cmap='gray', interpolation='none') #CAMBIAR
    plt.title("{}, {}".format(riesgos[predicted_classes[correct]],
                                                    riesgos[test_Y[correct]]))

    plt.tight_layout()
    plt.show()
```

```
incorrect = np.where(predicted_classes!=test_Y)[0]
print("Found %d incorrect labels" % len(incorrect))
for i, incorrect in enumerate(incorrect[0:9]):
    plt.subplot(3,3,i+1)
    plt.imshow(test_X[incorrect].reshape(28,28,3), cmap='gray', interpolation='none')  #CAMBIAR
    plt.title("{}, {}".format(riesgos[predicted_classes[incorrect]],
                                                    riesgos[test_Y[incorrect]]))
    plt.tight_layout()
    plt.show()
```

```
    target_names = ["Class {}".format(i) for i in range(nClasses)]
print(classification_report(test_Y, predicted_classes, target_names=target_names))
```

```
from tensorflow.keras.models import load_model
riesgo=['tornados','robo','inundaciones','incendio','asalto']
riesgos_model = load_model("C:\\Users\\zamud\\Desktop\\cnn\\modelo\\riesgos_model.keras")
from skimage.transform import resize

images=[]
filenames = ['prueba/robo.jpeg']

for filepath in filenames:
    image = plt.imread(filepath,0)
    image_resized = resize(image, (28, 28),anti_aliasing=True,clip=False,preserve_range=True)
    images.append(image_resized)

X = np.array(images, dtype=np.uint8) #convierto de lista a numpy
test_X = X.astype('float32')
test_X = test_X / 255.

predicted_classes = riesgos_model.predict(test_X)

for i, img_tagged in enumerate(predicted_classes):
    img = plt.imread(filenames[i])
    plt.figure(figsize=(3,3))
    plt.imshow(img)
    plt.title(f"Riesgo: robo Predicción: {riesgo[img_tagged.tolist().index(max(img_tagged))]}")
    plt.show()
```