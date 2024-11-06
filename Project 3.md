# Control DC Motor Using IR Remote

```C
#include <IRremote.hpp>


const int IR_RECEIVE_PIN = 10;  // Pin for IR receiver
unsigned long lastPressTime = 0;
const unsigned long debounceDelay = 300;


// Motor connections
int enA = 9;  // Enable pin for motor A
int in1 = 8;  // Input pin 1 for motor A
int in2 = 7;  // Input pin 2 for motor A


int mSpeed = 255;  // Initial motor speed


void setup()
{
  Serial.begin(9600);
  delay(500);  // Allow IR receiver to initialize
  IrReceiver.begin(IR_RECEIVE_PIN, ENABLE_LED_FEEDBACK);  // Start the receiver


  // Set all pins for motor control
  pinMode(enA, OUTPUT);
  pinMode(in1, OUTPUT);
  pinMode(in2, OUTPUT);


  // Turn off motor initially
  digitalWrite(in1, LOW);
  digitalWrite(in2, LOW);
}


void loop()
{
  if (IrReceiver.decode())
  {
    unsigned long currentTime = millis();


    // Check if enough time has passed since the last button press
    if (currentTime - lastPressTime > debounceDelay) {
      lastPressTime = currentTime;  // Update last press time


      // Print raw data for debugging
      // Serial.println(IrReceiver.decodedIRData.decodedRawData, HEX);  


      // Handle specific IR values using a switch statement
      switch (IrReceiver.decodedIRData.decodedRawData)
      {
        case 0xE619FF00:
          Serial.println("0");
          break;
        case 0xBA45FF00:
          Serial.println("1");
          break;
        case 0xB946FF00:
          Serial.println("2");
          break;
        case 0xB847FF00:
          Serial.println("3");
          break;
        case 0xBB44FF00:
          Serial.println("4");
          break;
        case 0xBF40FF00:
          Serial.println("5");
          break;
        case 0xBC43FF00:
          Serial.println("6");
          break;
        case 0xF807FF00:
          Serial.println("7");
          break;
        case 0xEA15FF00:
          Serial.println("8");
          break;
        case 0xF609FF00:
          Serial.println("9");
          break;
        case 0xE31CFF00:  // Code for stopping (example)
          Serial.println("Stopping");
          stopMotor();
          break;
        case 0xE718FF00:  // Code for increasing speed (example)
          Serial.println("Increasing Speed");
          increaseSpeed();
          break;
        case 0xAD52FF00:  // Code for decreasing speed (example)
          Serial.println("Decreasing Speed");
          decreaseSpeed();
          break;
        case 0xA55AFF00:  // Move Right
          Serial.println("Moving Right");
          moveForward();
        break;
        case 0xF708FF00:  // Move Left
          Serial.println("Moving Left");
          moveBackward();
        break;
        default:
          Serial.println("Received unknown button");
          break;
      }
    }
    IrReceiver.resume();  // Enable receiving the next value
  }
}


// Function to move the motor forward
void moveForward()
{
  digitalWrite(in1, HIGH);
  digitalWrite(in2, LOW);
  analogWrite(enA, mSpeed);  // Set speed for motor A
}


// Function to move the motor backward
void moveBackward()
{
  digitalWrite(in1, LOW);
  digitalWrite(in2, HIGH);
  analogWrite(enA, mSpeed);  // Set speed for motor A
}


// Function to stop the motor
void stopMotor()
{
  digitalWrite(in1, LOW);
  digitalWrite(in2, LOW);
}


// Function to increase motor speed
void increaseSpeed()
{
  mSpeed += 15;  // Increase speed by 15
  if (mSpeed > 255) {
    mSpeed = 255;  // Cap speed at 255
  }
  analogWrite(enA, mSpeed);  // Update speed for motor A
}


// Function to decrease motor speed
void decreaseSpeed()
{
  mSpeed -= 15;  // Decrease speed by 15
  if (mSpeed < 0) {
    mSpeed = 0;  // Prevent negative speed
  }
  analogWrite(enA, mSpeed);  // Update speed for motor A
}
```
