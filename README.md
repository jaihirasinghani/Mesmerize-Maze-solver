# Mesmerize-Maze-solver
# Maze Solving Bot with PID Control

This project showcases a maze-solving robot that uses an Arduino Nano, QTR sensors, and a motor driver to navigate through a maze. The robot follows lines and makes decisions at intersections using a Proportional-Integral-Derivative (PID) control algorithm. It can also detect dead ends and intersections to navigate through the maze efficiently.

# Components

Arduino Nano: The main microcontroller used to control the robot.

QTR Sensors: Infrared sensors to detect lines on the ground for navigation.

L298N Motor Driver: Controls the two DC motors based on input from the Arduino.

DC Motors: Two motors that drive the robot's wheels.

Buttons: For calibration and starting the maze-solving process.

Buck Converter: Converts input voltage to 5V for powering the Arduino and sensors.

Boost Converter: Increases voltage to 12V to power the motors.

# Overview of the Circuit

The QTR sensors are connected to the Arduino Nano to read line positions.

The L298N motor driver controls the motors, enabling the robot to turn and move forward or backward.

Buttons are used to initiate calibration and start the maze-solving process.

Power is regulated using buck and boost converters, ensuring appropriate voltages for the Arduino and motors.


# Code Breakdown

# Pin Definitions

The pin definitions for the motor driver, sensors, and buttons are as follows:

#define PWMA_PIN 6
#define PWMB_PIN 5
#define AIN1_PIN 7
#define AIN2_PIN 4
#define BIN1_PIN 3
#define BIN2_PIN 2
#define STBY_PIN 12
#define BUTTON_CALIBRATION_PIN 8
#define BUTTON_START_PIN 11

# PID Control

The robot uses PID control to adjust the speed of the motors based on the position of the line, which helps maintain accurate tracking along the line in the maze. The following constants are defined for PID tuning:

#define kp 0.83
#define kd 3.4
#define ki 0.0

# Maze Navigation

The robot identifies intersections and dead ends using the QTR sensors.

It decides on the direction to turn at intersections using predefined rules (left-hand or right-hand rule).

The simplify_path() function optimizes the path by eliminating unnecessary turns.


# Functions

setup(): Initializes the sensors and motors, calibrates the sensors, and selects the navigation rule.

PID_line(): Implements PID control for line following.

mspeed(): Controls motor speeds.

turn(): Executes turns based on the direction selected.

simplify_path(): Simplifies the path by combining consecutive turns into a single action.


# Getting Started

1. Hardware Setup: Assemble the circuit as shown in the diagram.


2. Software Setup: Upload the code to the Arduino Nano using the Arduino IDE.


3. Calibration: Press the calibration button to calibrate the sensors.


4. Start: Press the start button to begin solving the maze.


# Maze Solving Algorithm

The maze-solving robot utilizes a combination of line-following and intersection detection to navigate through a maze. The algorithm employs a Left-Hand or Right-Hand Rule to make decisions at intersections, ensuring that the robot can find its way through the maze. Additionally, it simplifies the path taken to optimize the route.

# 1. Line Following with PID Control

The robot follows a line using a PID (Proportional-Integral-Derivative) control algorithm, which adjusts the speed of the motors to keep the robot centered on the line. The following steps are involved:

Reading Sensor Values: The QTR sensors detect the line's position relative to the robot.

Calculating Error: The error is determined by the difference between the desired position (center of the line) and the actual position detected by the sensors.

PID Computation: The error is used to calculate the control output, which consists of:

Proportional Term (P): Reacts to the current error.

Integral Term (I): Reacts to the accumulation of past errors.

Derivative Term (D): Reacts to the rate of change of the error.


Adjusting Motor Speeds: The computed control output adjusts the speed of the left and right motors to correct the robot’s path.


void PID_line() {
    uint16_t position = qtr.readLineBlack(values);
    int error = 3500 - position;
    int P = error;
    int I = I + error;
    int D = error - lastError;
    lastError = error;
    int motorSpeed = P * kp + I * ki + D * kd;
    int rightMotorSpeed = basespeedR + motorSpeed;
    int leftMotorSpeed = basespeedL - motorSpeed;
   
    rightMotorSpeed = constrain(rightMotorSpeed, 0, maxspeedR);
    leftMotorSpeed = constrain(leftMotorSpeed, 0, maxspeedL);
   
    mspeed(rightMotorSpeed, leftMotorSpeed);
}

# 2. Intersection Detection

The robot detects intersections or dead ends by continuously reading sensor values. When no line is detected (all sensors read white), it indicates a dead end. If lines are detected on the left or right sides, it indicates an intersection.

if (values[5] < thresholdWhite && values[4] < thresholdWhite && values[3] < thresholdWhite && values[2] < thresholdWhite) {
    break; // Dead end
}
if ((values[7] > thresholdBlack && values[6] > thresholdBlack) || (values[1] > thresholdBlack && values[0] > thresholdBlack)) {
    break; // Intersection
}

# 3. Decision Making at Intersections

The robot makes decisions at intersections based on the selected rule (Left-Hand or Right-Hand). This is determined by checking the state of specific buttons during setup:

Left-Hand Rule (LHR): The robot prioritizes turning left at intersections.

Right-Hand Rule (RHR): The robot prioritizes turning right at intersections.


The selected rule is used in functions select_turnL() and select_turnR() to determine the direction the robot should turn when it encounters an intersection.

unsigned char select_turnL(unsigned char found_left, unsigned char found_straight, unsigned char found_right) {
    if (found_left) return 'L';
    if (found_straight) return 'S';
    if (found_right) return 'R';
    return 'B';
}

# 4. Path Simplification

To optimize the robot's path through the maze, the algorithm simplifies the path by combining consecutive turns into simpler movements. For example, if the robot makes a right turn followed by another right turn, this can be simplified into a single U-turn.

void simplify_path() {
    if (path_length < 3 || path[path_length - 2] != 'B') return;
   
    int total_angle = 0;
    for (int p = 1; p <= 3; p++) {
        switch (path[path_length - p]) {
            case 'R': total_angle += 90; break;
            case 'L': total_angle += 270; break;
            case 'B': total_angle += 180; break;
        }
    }
   
    total_angle %= 360;
   
    switch (total_angle) {
        case 0: path[path_length - 3] = 'S'; break;
        case 90: path[path_length - 3] = 'R'; break;
        case 180: path[path_length - 3] = 'B'; break;
        case 270: path[path_length - 3] = 'L'; break;
    }
   
    path_length -= 2;
}

# 5. Loop Execution

The loop() function continuously executes the following:

It follows the line using PID control until an intersection or dead end is detected.

At intersections, it decides the next direction based on the selected rule.

It updates the path and simplifies it after each decision.


void loop() {
    while (1) {
        while (1) {
            PID_line();
            qtr.readLineBlack(values);
           
            if (values[5] < thresholdWhite && values[4] < thresholdWhite && values[3] < thresholdWhite && values[2] < thresholdWhite) {
                break; // Dead end
            }
           
            if ((values[7] > thresholdBlack && values[6] > thresholdBlack) || (values[1] > thresholdBlack && values[0] > thresholdBlack)) {
                break; // Intersection
            }
        }
       
        mspeed(0, 0);
        delay(300);
       
        unsigned char found_left = 0, found_straight = 0, found_right = 0;
        qtr.readLineBlack(values);
       
        if (values[0] > thresholdBlack && values[1] > thresholdBlack) {
            found_left = 1;
        }
       
        if (values[7] > thresholdBlack && values[6] > thresholdBlack) {
            found_right = 1;
        }
       
        mspeed(basespeedR, basespeedL);
        delay(450);
        mspeed(0, 0);
       
        qtr.readLineBlack(values);
        if (values[1] > thresholdBlack || values[2] > thresholdBlack || values[3] > thresholdBlack || values[4] > thresholdBlack || values[5] > thresholdBlack || values[6] > thresholdBlack) {
            found_straight = 1;
        }
       
        mspeed(basespeedR, basespeedL);
        delay(100);
        mspeed(0, 0);
       
        char dir;
        if (hand == 1) dir = select_turnL(found_left, found_straight, found_right);
        else dir = select_turnR(found_right, found_straight, found_left);
       
        turn(dir);
        mspeed(0, 0);
        path[path_length] = dir;
        path_length++;
        simplify_path();
    }
}

# Future Improvements

Implementing more complex algorithms for faster maze solving.

Adding a display to show the path taken by the robot.

Enhancing the PID control for smoother navigation.



