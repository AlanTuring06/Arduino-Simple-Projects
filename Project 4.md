# Control RGB LED Using IR Remote

```C
#include <IRremote.hpp>

const int IR_RECEIVE_PIN = 8;  // Pin for IR receiver
unsigned long lastPressTime = 0;
const unsigned long debounceDelay = 300;

int redPin = 9;
int bluePin = 10;
int greenPin = 11;

int rBright = 255;
int gBright = 255;
int bBright = 255;

float intensity = 1.0;

void setup()
{
  Serial.begin(9600);
  delay(500);  // Allow IR receiver to initialize
  IrReceiver.begin(IR_RECEIVE_PIN, ENABLE_LED_FEEDBACK);  // Start the receiver

  // Set all pins for motor control
  pinMode(redPin, OUTPUT);
  pinMode(greenPin, OUTPUT);
  pinMode(bluePin, OUTPUT);
}

void loop()
{
  if (IrReceiver.decode())
  {
    unsigned long currentTime = millis();

    // Check if enough time has passed since the last button press
    if (currentTime - lastPressTime > debounceDelay) {
      lastPressTime = currentTime;  // Update last press time

      // Handle specific IR values using a switch statement
      switch (IrReceiver.decodedIRData.decodedRawData)
      {
        case 0xE619FF00:
          Serial.println("Red");
          setColor(255, 0, 0); // Red Color
          break;
        case 0xBA45FF00:
          Serial.println("Green");
          setColor(0, 255, 0); // Green Color
          break;
        case 0xB946FF00:
          Serial.println("Blue");
          setColor(0, 0, 255); // Blue Color
          break;
        case 0xB847FF00:
          Serial.println("White");
          setColor(255, 255, 255); // White Color
          break;
        case 0xBB44FF00:
          Serial.println("Orange");
          setColor(255, 128, 0); // Orange Color
          break;
        case 0xBF40FF00:
          Serial.println("Yellow");
          setColor(255, 255, 0); // Yellow Color
          break;
        case 0xBC43FF00:
          Serial.println("Aqua");
          setColor(0, 255, 255); // Aqua Color
          break;
        case 0xF807FF00:
          Serial.println("Purple");
          setColor(255, 0, 255); // Purple Color
          break;
        case 0xEA15FF00:
          Serial.println("Navy");
          setColor(0, 0, 128); // Navy Color
          break;
        case 0xF609FF00:
          Serial.println("No color");
          setColor(0, 0, 0);
          break;
        case 0xE31CFF00:  // OK
          Serial.println("OK");
          break;
        case 0xE718FF00:  // Increase intensity
          Serial.println("Increasing Intensity");
          up();
          break;
        case 0xAD52FF00:  // Decrease intensity
          Serial.println("Decreasing Intensity");
          down();
          break;
        default:
          Serial.println("Received unknown button");
          break;
      }

      updateLEDs();
    }
    IrReceiver.resume();  // Enable receiving the next value
  }
}

void up()
{
  intensity *= 1.3;
  if (intensity > 1.0) {
    intensity = 1.0;
  }
  updateLEDs();
}

void down()
{
  intensity *= 0.75;
  updateLEDs();
}

void setColor(int redValue, int greenValue, int blueValue)
{
  rBright = redValue;
  gBright = greenValue;
  bBright = blueValue;
}

void updateLEDs()
{
  analogWrite(redPin, rBright * intensity);
  analogWrite(greenPin, gBright * intensity);
  analogWrite(bluePin, bBright * intensity);
}

```