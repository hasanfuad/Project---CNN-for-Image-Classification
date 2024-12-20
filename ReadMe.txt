Image Classification Using CNN

This repository contains code to build a Convolutional Neural Network (CNN) using TensorFlow's Keras API to classify images of cats and dogs.

###Project Overview

## Objectives

Train a CNN on a dataset of 4000 cat images and 4000 dog images.

Evaluate the model using a test dataset of 1000 cat and 1000 dog images.

Predict whether an image in the "single_prediction" folder is a cat or a dog.

## Key Features

Image preprocessing using normalization and data augmentation.

CNN architecture with convolutional, pooling, and fully connected layers.

Training, evaluation, and prediction functionalities.

##Getting Started

Prerequisites

Ensure you have Python and TensorFlow installed. Use the following command to install TensorFlow:

	pip install tensorflow

	Directory Structure

##The project assumes the following directory structure:

dataset/
    training_set/
        cats/
        dogs/
    test_set/
        cats/
        dogs/
single_prediction/
    image1.jpg
    image2.jpg

##Steps to Run the Project

1. Data Preprocessing

Data preprocessing includes normalization and augmentation for the training set. 
Here's how it’s done:

from tensorflow.keras.preprocessing.image import ImageDataGenerator

train_datagen = ImageDataGenerator(
    rescale=1./255,
    shear_range=0.2,
    zoom_range=0.2,
    horizontal_flip=True
)
training_set = train_datagen.flow_from_directory(
    'dataset/training_set',
    target_size=(64, 64),
    batch_size=32,
    class_mode='binary'
)

test_datagen = ImageDataGenerator(rescale=1./255)
test_set = test_datagen.flow_from_directory(
    'dataset/test_set',
    target_size=(64, 64),
    batch_size=32,
    class_mode='binary'
)

2. Building the CNN

The CNN consists of convolutional and pooling layers, followed by flattening and fully connected layers:

import tensorflow as tf

cnn = tf.keras.models.Sequential()

cnn.add(tf.keras.layers.Conv2D(filters=32, kernel_size=3, activation='relu', input_shape=[64, 64, 3]))
cnn.add(tf.keras.layers.MaxPooling2D(pool_size=2, strides=2))

cnn.add(tf.keras.layers.Conv2D(filters=64, kernel_size=3, activation='relu'))
cnn.add(tf.keras.layers.MaxPooling2D(pool_size=2, strides=2))

cnn.add(tf.keras.layers.Flatten())
cnn.add(tf.keras.layers.Dense(units=128, activation='relu'))
cnn.add(tf.keras.layers.Dense(units=1, activation='sigmoid'))

3. Compiling and Training the Model

cnn.compile(optimizer='adam', loss='binary_crossentropy', metrics=['accuracy'])

cnn.fit(x=training_set, validation_data=test_set, epochs=25)

4. Saving the Model

cnn.save("cat_dog_classifier.keras")

5. Predicting New Images

To classify images in the "single_prediction" folder:

from tensorflow.keras.preprocessing import image
import numpy as np

def classify_image(image_path):
    test_image = image.load_img(image_path, target_size=(64, 64))
    test_image = image.img_to_array(test_image)
    test_image = np.expand_dims(test_image, axis=0) / 255.0
    result = cnn.predict(test_image)
    return 'dog' if result[0][0] > 0.5 else 'cat'

import os
for filename in os.listdir('single_prediction'):
    print(f"{filename}: {classify_image(os.path.join('single_prediction', filename))}")

Results

Training Accuracy: Approximately 95% after 25 epochs.

Test Accuracy: Approximately 92%.

##For more optimization:

Add more training data for better generalization.

Implement a web interface to upload and classify images.

Use transfer learning with pre-trained models for improved accuracy.