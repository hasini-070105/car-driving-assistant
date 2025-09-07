# car-driving-assistant
The Car Driving Assistant System is an innovative project designed to enhance vehicle safety and driving convenience.It uses ultrasonic sensors to measure the distance on both sides of the car, enabling the vehicle to take automatic actions based on these measurements. The system aims to assist the driver by providing real-time distance data and facilitating automatic turning maneuver.


/ Arduino code for a car that adjusts its direction based on road curvature using ultrasonic
// sensors and only rear (back) motors

#include<Servo.h>

#define lag 5000 // delay between action
#define change 20 // change of the angle

// Pin assignments
const int trigLeft = 2;
const int echoLeft = 3;
const int trigRight = 4;
const int echoRight = 5;
const int servopin = 9;
const int leftled = 10;
const int rightled = 11;

Servo myservo;
/ Function to measure distance using ultrasonic sensor
long measureDistance(int trigPin, int echoPin) {
 digitalWrite(trigPin, LOW);
 delayMicroseconds(2);
 digitalWrite(trigPin, HIGH);
 delayMicroseconds(10);
 digitalWrite(trigPin, LOW);
 long duration = pulseIn(echoPin, HIGH);
 long distance = duration * 0.034 / 2; // Convert to cm
 return distance;
}

// Functions to control rear motors
void moveForward() {
 myservo.write(70);
 delay(lag);
}
void turnLeft() {
    myservo.write(70 - change);
    digitalWrite(leftled, HIGH);
    delay(lag);
    digitalWrite(leftled, LOW);
    myservo.write(70);
}

void turnRight() {
    myservo.write(70 + change);
    digitalWrite(rightled, HIGH);
    delay(lag);
    digitalWrite(rightled, LOW);
    myservo.write(70);
}

void setup() {
    Serial.begin(9600);

    pinMode(trigLeft, OUTPUT);
    pinMode(echoLeft, INPUT);
    pinMode(trigRight, OUTPUT);
    pinMode(echoRight, INPUT);
}
void loop() {
    long distanceLeft = measureDistance(trigLeft, echoLeft);
    long distanceRight = measureDistance(trigRight, echoRight);

    Serial.print("Left: ");
    Serial.print(distanceLeft);
    Serial.print(" cm, Right: ");
    Serial.print(distanceRight);
    Serial.println(" cm");

    int difference = distanceLeft - distanceRight;

    if (difference < 0) {
        turnRight();  // Move straight using rear motors
    } else if (difference > 0) {
        turnLeft();   // More space on left, turn right
    } else {
        moveForward(); // More space on right, turn left
    }
}



