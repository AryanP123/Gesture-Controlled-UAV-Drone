# Gesture-Controlled-UAV-Drone
 Controlling a drone using hand gestures and an IMU sensor.

# What is the project about? 

The objective of this project is to control the UAV via different hand gestures. I wanted to be able to control the drone to move in 7 different directions, which are left, right, up, down, backwards, forwards, and landing as well as 1 trick which is doing a flip. 

# What is the purpose of doing this project? 

This project allows me to implement a complete system from gathering raw data to translating that data into tangible results, which I believe is a great learning experience that will be useful in the future. Another reason why I was drawn to this project was because I have never programmed any type of UAV before so I was excited to learn more about programming and controlling a drone.

# Why is the proposed project useful?

This project is useful because it makes it easier and more intuitive to control drones, thus making it more accessible for people to leverage the capabilities of drones. Also in some scenarios, such as those that require quick reaction time, being able to to control the drones without looking at a screen can be useful.

# Project Overview 
- Hardware:
 - MCI: ESP32-S3
 - IMU: MPU6050
 - Tello Drone
- Software
 - C for developing the MCI program for printing accelerometer and gyroscope data.
 - Python for developing various scripts for remaining elements of the pipeline.
  - Recording
  - Data trimming
  - Data partitioning
  - Model training and evaluation
  - Data streaming and classification

# Setup Instructions
- Move the IMU folder into your dedicated esp folder
- Build and flash the code onto the ESP32

The submitted code includes the data necessary for training and testing the model and also a model that has already been trained upon the collected data.

As a result, if you would like to test the live classification on the drone, simply run the final_stream.py file. If you would simply test the live classification without connecting to the drone, comment out all lines involving Tello commands and run the final_stream.py file.

If you would like to test the model training, please delete the ‘model.pkl’ file in the root directory, then run the final_svm.py to train and evaluate a new model.

If you would like to test the data collection process, please delete all files within the ‘.../data’ directory. Then, run final_record.py to record new data (ensure an equal number of each gesture before proceeding). You can then run final_trim_data.py to clean up the data and prepare it for training, then run final_csv.py to generate csv files to partition the dataset into a training set and a testing set. Finally, you may run the final_svm.py file to train the classifier model and evaluate it, then the final_stream.py file to test the live classification.

<img width="568" alt="image" src="https://github.com/user-attachments/assets/1e2a4a01-f564-4d7f-8503-b3df73e126f9" />

# Data Collection
First, the i2c_simple_main.c file collects acceleration and gyroscopic data on the x, y and z axis at a sampling rate of 100 hertz. This program is built and flashed to the ESP. The final_record.py file is used to take in this serial input and format it into text files, collecting data for 1 second per data point. final_trim_data.py is used to ensure all of the data is equal lengths and properly formatted to prevent errors during model training. final_csv.py is used to partition the dataset into the training and testing set, by listing the associated file names with each set in their respective text and csv files.

# Model Training and Evaluation
final_svm.py loads each text file’s data points’ accelerometer and gyroscope rotational data as features along with its gesture classification as its label. These features are first normalized, then utilized to train and evaluate the SVM classifier, yielding both the test accuracy and a confusion matrix. The model is then saved to the user’s device, for use during the live classification.

# Live Classification and Drone Movement 
Similarly to the recording file, final_stream.py uses pyserial to read in the accelerometer and gyroscope data being sent by the ESP. However, a deque is first initialized to the length of the data points, to ensure the amount of live input data is equal to the amount of data inputted during training. The model trained previously is also loaded.

The serial input data is formatted identically to the data included in the dataset. Once the deque is full, it signifies there is enough data for classification, so the data is normalized then inputted into the model. The model’s prediction is outputted and the deque is cleared.

Based on the classified gesture, the corresponding movement command is sent to the drone via WiFi connection using the djitellopy library. A short cooldown period is then initialized until the next gesture can be read and classified.

# Graphical Analysis
final_fft.py may be used to generate figures of both raw data and FFT-transformed data for further analysis of each gesture type. This was utilized in our reflection process to understand shortcomings in our model’s ability to distinguish gestures.

<img width="313" alt="image" src="https://github.com/user-attachments/assets/d2e57512-9cff-4d53-9c61-ca1f98e158b8" />

With a dataset of 495 files, I achieved an SVM test accuracy of 97.980%.

# Demo
https://www.youtube.com/watch?v=xzMobrAIKGE
[![ESP32-S3 / IMU - Based Gesture Controlled UAV Drone](https://img.youtube.com/vi/xzMobrAIKGE/0.jpg)](https://www.youtube.com/watch?v=xzMobrAIKGE)

