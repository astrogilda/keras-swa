# Keras SWA - Stochastic Weight Averaging

[![PyPI version](https://badge.fury.io/py/keras-swa.svg)](https://pypi.python.org/pypi/keras-swa/) 
[![License](https://img.shields.io/badge/license-MIT-blue.svg)](https://github.com/simon-larsson/keras-swa/blob/master/LICENSE)

This is an implemention of SWA for Keras and TF-Keras.

## Introduction
Stochastic weight averaging (SWA) is build upon the same principle as [snapshot ensembling](https://arxiv.org/abs/1704.00109) and [fast geometric ensembling](https://arxiv.org/abs/1802.10026). The idea is that averaging select stages of training can lead to better models. Where as the two former methods average by sampling and ensembling models, SWA instead average weights. This has been shown to give comparable improvements confined into a single model.

[![Illustration](https://raw.githubusercontent.com/simon-larsson/keras-swa/master/swa_illustration.png)](https://raw.githubusercontent.com/simon-larsson/keras-swa/master/swa_illustration.png)

## Paper
 - Title: Averaging Weights Leads to Wider Optima and Better Generalization
 - Link: https://arxiv.org/abs/1803.05407
 - Authors: Pavel Izmailov, Dmitrii Podoprikhin, Timur Garipov, Dmitry Vetrov, Andrew Gordon Wilson
 - Repo: https://github.com/timgaripov/swa (PyTorch)

## Installation

    pip install keras-swa
    
### SWA

Keras callback object for SWA.  

#### Arguments
`swa_epochs` - The number of epochs in the end of training where SWA is applied.

`verbose` - Verbosity mode, 0 or 1.
    
#### Example

For Keras
```python
from sklearn.datasets.samples_generator import make_blobs
from keras.utils import to_categorical
from keras.models import Sequential
from keras.layers import Dense
from keras.optimizers import SGD

from swa.keras import SWA
 
# make dataset
X, y = make_blobs(n_samples=1000, 
                  centers=3, 
                  n_features=2, 
                  cluster_std=2, 
                  random_state=2)

y = to_categorical(y)

# build model
model = Sequential()
model.add(Dense(50, input_dim=2, activation='relu'))
model.add(Dense(3, activation='softmax'))

model.compile(loss='categorical_crossentropy', 
              optimizer=SGD(learning_rate=0.1))

epochs = 100
start_epoch = 75

# define swa callback
swa = SWA(start_epoch=start_epoch, 
          lr_schedule='constant', 
          swa_lr=0.01, 
          verbose=1)

# train
model.fit(X, y, epochs=epochs, verbose=1, callbacks=[swa])
```

Or for Keras in Tensorflow

```python
from sklearn.datasets.samples_generator import make_blobs
from tensorflow.keras.utils import to_categorical
from tensorflow.keras.models import Sequential
from tensorflow.keras.layers import Dense
from tensorflow.keras.optimizers import SGD

from swa.tfkeras import SWA

# make dataset
X, y = make_blobs(n_samples=1000, 
                  centers=3, 
                  n_features=2, 
                  cluster_std=2, 
                  random_state=2)

y = to_categorical(y)

# build model
model = Sequential()
model.add(Dense(50, input_dim=2, activation='relu'))
model.add(Dense(3, activation='softmax'))

model.compile(loss='categorical_crossentropy', 
              optimizer=SGD(learning_rate=0.1))

epochs = 100
start_epoch = 75

# define swa callback
swa = SWA(start_epoch=start_epoch, 
          lr_schedule='constant', 
          swa_lr=0.01, 
          verbose=1)

# train
model.fit(X, y, epochs=epochs, verbose=1, callbacks=[swa])
```

Output
```
Epoch 1/100
1000/1000 [==============================] - 1s 703us/step - loss: 0.7518
Epoch 2/100
1000/1000 [==============================] - 0s 47us/step - loss: 0.5997
...
Epoch 74/100
1000/1000 [==============================] - 0s 31us/step - loss: 0.3913
Epoch 75/100

Epoch 00075: starting stochastic weight averaging
1000/1000 [==============================] - 0s 202us/step - loss: 0.3907
Epoch 76/100
1000/1000 [==============================] - 0s 47us/step - loss: 0.3911
...
Epoch 99/100
1000/1000 [==============================] - 0s 31us/step - loss: 0.3910
Epoch 100/100
1000/1000 [==============================] - 0s 47us/step - loss: 0.3905

Epoch 00100: final model weights set to stochastic weight average
```
