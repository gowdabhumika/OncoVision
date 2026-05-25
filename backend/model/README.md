import tensorflow as tf

from tensorflow.keras.models import Sequential

from tensorflow.keras.layers import Conv2D
from tensorflow.keras.layers import MaxPooling2D
from tensorflow.keras.layers import Flatten
from tensorflow.keras.layers import Dense
from tensorflow.keras.layers import Dropout
from tensorflow.keras.layers import BatchNormalization

# Image Size
IMG_SIZE = 224

# Create CNN Model
model = Sequential()

# First Convolution Layer
model.add(
    Conv2D(
        32,
        (3,3),
        activation='relu',
        input_shape=(IMG_SIZE, IMG_SIZE, 3)
    )
)

model.add(BatchNormalization())

model.add(MaxPooling2D(2,2))

# Second Convolution Layer
model.add(
    Conv2D(
        64,
        (3,3),
        activation='relu'
    )
)

model.add(BatchNormalization())

model.add(MaxPooling2D(2,2))

# Third Convolution Layer
model.add(
    Conv2D(
        128,
        (3,3),
        activation='relu'
    )
)

model.add(BatchNormalization())

model.add(MaxPooling2D(2,2))

# Fourth Convolution Layer
model.add(
    Conv2D(
        256,
        (3,3),
        activation='relu'
    )
)

model.add(BatchNormalization())

model.add(MaxPooling2D(2,2))

# Flatten Layer
model.add(Flatten())

# Dense Layer
model.add(
    Dense(
        512,
        activation='relu'
    )
)

# Dropout
model.add(Dropout(0.5))

# Output Layer
# 0 = No Cancer
# 1 = Cancer Detected
model.add(
    Dense(
        1,
        activation='sigmoid'
    )
)

# Compile Model
model.compile(
    optimizer='adam',
    loss='binary_crossentropy',
    metrics=['accuracy']
)

# Show Model Summary
model.summary()

# Save Model
model.save(
    'saved_model/cancer_model.h5'
)

print('AI Cancer Detection Model Saved')
