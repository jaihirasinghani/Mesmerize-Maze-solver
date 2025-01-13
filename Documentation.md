# Maze-Solving Bot Documentation

# Project Overview

This project involves the design and development of a maze-solving robot capable of autonomously navigating through a maze using a line-following approach. The robot is built using an Arduino Nano microcontroller, a set of infrared sensors, motor drivers, and other electronic components. The maze-solving algorithm uses a combination of PID control for line following and decision-making at intersections to navigate the maze efficiently.

# Hardware Components
Arduino Nano: The central microcontroller that runs the robot's software.

QTR-8RC Reflectance Sensor Array: An array of infrared sensors used to detect the line and intersections in the maze.

L298N Motor Driver: Used to control the speed and direction of the DC motors.

DC Motors: Drive the robot's wheels.

Power Supply: A battery pack to power the robot.

Buck and Boost Converters: Used to regulate the voltage for the Arduino and motors.

Push Buttons: For calibration and starting the robot.

LED Indicators: Used to display the status during operation.


# Stepwise Circuit Preparation

# 1. Power Supply Setup:

Connect the battery pack's positive and negative terminals to the input of the buck converter.

Set the buck converter to output 5V and connect its output to the 5V and GND pins of the Arduino Nano.



# 2. Boost Converter:

Connect the output of the buck converter (5V) to the input of the boost converter.

Set the boost converter to output 12V, which will power the motors.


# 3. Motor Driver:

Connect the output of the boost converter to the 12V and GND pins on the L298N motor driver.

Connect the motor driver’s input pins (IN1, IN2, IN3, IN4) to digital pins on the Arduino Nano.

Connect the PWM control pins (ENA and ENB) of the motor driver to PWM-capable digital pins on the Arduino.

Attach the DC motors to the motor driver’s output pins.


# 4. QTR Sensor Array:

Connect the QTR sensor array's sensor pins to the analog pins on the Arduino Nano.

Connect the power pins of the QTR sensor array to the 5V and GND pins on the Arduino Nano.


# 5. Push Buttons and LEDs:

Connect one push button to a digital pin on the Arduino Nano for calibration (e.g., D8).

Connect another push button to a different digital pin for starting the robot (e.g., D11).

Connect LEDs to digital pins on the Arduino Nano with appropriate resistors to limit the current.


# 6. Standby Pin:

Connect the motor driver’s standby pin to a digital pin on the Arduino Nano, enabling control of the motor driver’s standby mode.

# Software Components

# Arduino Code

The Arduino code is structured into several functions that handle different aspects of the robot's operation:

1. Setup Function: Initializes the sensors, motors, and pins, and calibrates the QTR sensors. The user must press a button to begin calibration, during which the robot moves forward and backward to get readings from the sensors.


2. PID Control: The PID_line() function reads the sensor values and calculates the error based on the robot's position relative to the line. It then adjusts the motor speeds using a PID controller to keep the robot centered on the line.


3. Decision Making: At intersections, the robot decides which direction to turn based on the maze-solving strategy. The turn() function executes the turn based on the direction chosen.


4. Path Simplification: The simplify_path() function optimizes the path by simplifying unnecessary turns and backtracking, which helps the robot navigate the maze more efficiently in subsequent runs.


5. Main Loop: The loop() function continuously reads sensor values, follows the line using PID control, and makes decisions at intersections.



# Algorithm

The maze-solving algorithm follows these steps:

1. Line Following: The robot uses PID control to follow the line.


2. Intersection Detection: When the robot detects an intersection, it decides whether to turn left, right, or go straight based on its current position and the maze's layout.


3. Path Simplification: After completing a segment of the maze, the robot simplifies its path to improve efficiency.


4. Maze Solving: The robot keeps track of its path and uses it to backtrack or find the shortest path to the goal in subsequent runs.



# Calibration Process

1. Place the robot at the starting position.


2. Press the calibration button to begin the process.


3. The robot will move forward and backward, calibrating its sensors to detect the line's threshold values.


4. After calibration, the robot will be ready to solve the maze.



# Maze Solving Process

1. Place the robot at the maze entrance and press the start button.


2. The robot will begin following the line, using the PID control algorithm.


3. At intersections, it will decide the best direction to turn based on the maze-solving strategy.


4. The robot will continue until it reaches the maze's end.


5. It will simplify its path and use the optimized path in subsequent runs for faster solving.



# Safety Precautions

Handle Electronics with Care: Ensure that all components, especially the Arduino Nano and motor driver, are handled carefully to avoid static discharge or physical damage.

Battery Safety: Be cautious when handling the battery pack. Ensure it is charged correctly and avoid short circuits. Never leave the battery connected to the circuit unattended for long periods.

Correct Wiring: Double-check all connections to avoid short circuits or incorrect wiring, which could damage the components or cause malfunctions.

Motor Power: Ensure the motor driver is powered off before making any changes to the motor connections to prevent accidental movement.

Heat Dissipation: Allow sufficient ventilation around the motor driver and boost converter to prevent overheating, especially during prolonged use.


# Troubleshooting

Robot Not Following Line: Ensure that the sensors are properly calibrated and the line is within the detectable range of the sensors.

Erratic Movements: Check the motor connections and ensure the motor driver is functioning correctly.

Incorrect Turns at Intersections: Verify the maze-solving algorithm logic and ensure the sensors are correctly detecting intersections.


# Enhancements

Implement additional algorithms like flood fill or A* for more complex maze-solving.

Add Bluetooth or Wi-Fi modules for remote control and monitoring.

Use more advanced sensors like LIDAR for better obstacle detection.
