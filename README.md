# DJI Tello Hand Gesture control

The main goal of this project is to control the drone using hand gestures without any gloves or additional equipment.
Just camera on the drone or your smartphone(soon), laptop and human hand.


## Index
1. [Introduction](#Introduction)
2. [Setup](#Setup)
    1. [Install pip packages](#1.-Installing-pip-packages)
    2. [Connect and test Tello](#2.-Connect-Tello)
3. [Usage](#Usage)
    * [Keyboard control](##Keyboard-control)
    * [Gesture control](#Gesture-control)
    * [Technical description](#Technical-details-of-gesture-detector)
4. [Repository structure](#Repository-structure)

## Introduction
This project relies on two main parts - DJI Tello drone and Mediapipe fast hand keypoints recognition.

DJI Tello is a perfect drone for any kind of programming experiments. It has a rich Python API (also Swift is available) which helps to almost fully control a drone and utilise its camera for Computer vision.

Mediapipe is an amazing ML platform with many robust solutions like Face mesh, Hand Keypoints detection and Objectron. Moreover, their model can be used on the mobile platforms with on-device acceleration.


## Setup
I would recommend to use a virtual python environment for this project. 

### 1. Installing pip packages
First, we need to install python dependencies. Make sure you that you are using `python3.6` or above.

List of packages
```sh
ConfigArgParse == 1.2.3
numpy == 1.19.3
opencv_python == 4.5.1.48
tensorflow == 2.4.1
mediapipe == 0.8.2
```

First install Python3 support from my Github respository [DJITelloPy3](https://github.com/shubhendu-ranadive/DJITelloPy3)

Then install the above list of packages
```sh
git clone https://github.com/shubhendu-ranadive/tello-gesture-control
cd tello-gesture-control
pip3 install -r requirements.txt
```
### 2. Connect Tello
Turn on drone and connect computer to its WiFi

Next, run the following code to verify connectivity

```sh
python3 tests/connection_test.py
```

On successful connection

```json
1. Connection test:
Send command: command
Response: b'ok'


2. Video stream test:
Send command: streamon
Response: b'ok'
```

If you get such output, you may need to check your connection with the drone

```json
1. Connection test:
Send command: command
Timeout exceed on command command
Command command was unsuccessful. Message: False


2. Video stream test:
Send command: streamon
Timeout exceed on command streamon
Command streamon was unsuccessful. Message: False
```

## Usage
There are 2 types of control: keyboard and gesture. You can change between control types during the flight. Below is a complete description of both types.

Run the following command to start the tello control :

```sh
python3 main.py
```

This script will start the python window with visualization like this:

<img width="60%" alt="window" src="https://user-images.githubusercontent.com/13486777/111294470-09d08480-8653-11eb-895d-a8ca9f6a288d.png">


### Keyboard control
To control the drone with your keyboard at any time - press the `k` key.

The following is a list of keys and action description -

* `k` -> Toggle Keyboard controls
* `g` -> Toggle Gesture controls
* `Space` -> Take off drone(if landed) **OR** Land drone(if in flight)
* `w` -> Move forward
* `s` -> Move back
* `a` -> Move left
* `d` -> Move right
* `e` -> Rotate clockwise
* `q` -> Rotate counter-clockwise
* `r` -> Move up
* `f` -> Move down
* `Esc` -> End program and land the drone 


### Gesture control 

By pressing `g` you activate gesture control mode. Here is a full list of gestures that are available now.

<img alt="gestures_list" width="80%" src="https://user-images.githubusercontent.com/13486777/110933057-f1035e80-8334-11eb-8458-988af973804e.JPG">

### Technical details of gesture detector
Mediapipe Hand keypoints recognition is returning 3D coordinated of 20 hand landmarks. For our
model we will use only 2D coordinates.

<img alt="gestures_list" width="80%" src="https://user-images.githubusercontent.com/13486777/110933339-49d2f700-8335-11eb-9588-5f68a2677ff0.png">


Then, these points are preprocessed for training the model in the following way.

<img alt="preprocessing" width="80%" src="https://user-images.githubusercontent.com/13486777/111294503-11902900-8653-11eb-9856-a50fe96e750e.png">


After that, we can use data to train our model. Keypoint classifier is a simple Neural network with such 
structure

<img alt="model_structure" width="80%" src="https://user-images.githubusercontent.com/13486777/112172879-c0a5a500-8bfd-11eb-85b3-34ccfa256ec3.jpg">


## Repository structure
<pre>
│  main.py
│  Keypoint_model_training.ipynb
│  config.txt
│  requirements.txt
│  
├─model
│  └─keypoint_classifier
│      │  keypoint.csv
│      │  keypoint_classifier.hdf5
│      │  keypoint_classifier.py
│      │  keypoint_classifier.tflite
│      └─ keypoint_classifier_label.csv
│ 
├─gestures
│   │  gesture_recognition.py
│   │  tello_gesture_controller.py
│   └─ tello_keyboard_controller.py
│          
├─tests
│   └─connection_test.py
│ 
└─utils
    └─cvfpscalc.py
</pre>
### main.py
Main script which controls the functionality of drone control and gesture recognition<br>

### keypoint_classification.ipynb
This is a model training script for hand sign recognition.

### model/keypoint_classifier
This directory stores files related to gesture recognition.<br>

* Training data(keypoint.csv)
* Trained model(keypoint_classifier.tflite)
* Label data(keypoint_classifier_label.csv)
* Inference module(keypoint_classifier.py)

### gestures/
This directory stores files related to drone controllers and gesture modules.<br>

* Keyboard controller (tello_keyboard_controller.py)
* Gesture controller(tello_keyboard_controller.py)
* Gesture recognition module(keypoint_classifier_label.csv)

### utils/cvfpscalc.py
Module for FPS measurement.

# TODO
- [ ] Motion gesture support (LSTM)
- [ ] Web UI for mobile on-device gesture control

# Reference
* [MediaPipe](https://github.com/google/mediapipe)
* [MediaPipe Hand gesture recognition (by Kazuhito00)](https://github.com/Kazuhito00/hand-gesture-recognition-using-mediapipe)
