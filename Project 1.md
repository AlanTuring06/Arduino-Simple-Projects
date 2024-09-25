# Use Pushbutton as a Toggle Switch

```C
int redPin = 8;          // LED pin
int buttonPin = 7;       // Button pin
int buttonState;         // Variable to store current button state
int lastButtonState = 1; // Variable to store the last button state
int ledState = 0;        // Variable to store the LED state (0 = OFF, 1 = ON)

void setup() {
  pinMode(buttonPin, INPUT);  // Set the button pin as input
  pinMode(redPin, OUTPUT);    // Set the LED pin as output
  Serial.begin(9600);
}

void loop() {
  // Read the button state
  buttonState = digitalRead(buttonPin);
  Serial.println(buttonState);

  // Check if the button state has changed from unpressed to pressed
  if (buttonState == 0 && lastButtonState == 1) {
    // Toggle the LED state
    ledState = !ledState;    // If ledState is 1, it becomes 0; if 0, it becomes 1
  }

  // Update the LED state based on the toggle
  digitalWrite(redPin, ledState);  // Write the LED state (1 or 0)
  
  // Save the current button state as the last state for the next loop
  lastButtonState = buttonState;
}
```

## Code Overview

This code toggles an LED on and off when a button is **pressed**, using the concept of **state change**. The key point here is the use of the `lastButtonState` variable to detect when the button has been **newly pressed**.

## Why `lastButtonState` = `buttonState`?
The variable `lastButtonState` is used to *track the previous state of the button* in order to detect a **state change** (i.e., when the button goes from **unpressed** to **pressed**).

Without `lastButtonState`, the code would constantly toggle the LED state as long as the button is **pressed**, because in every loop iteration, the button would still be read as **pressed**, and the LED would keep toggling rapidly.

By comparing `buttonState` to `lastButtonState`, the code ensures that the LED only toggles once when the button is **pressed**, rather than continuously while the button is held down. The **key condition** is:

```C
if (buttonState == 0 && lastButtonState == 1)
```
- **`buttonState` == 0**: The button is currently **pressed**.
- **`lastButtonState` == 1**: The button was previously **unpressed**.

When this condition is **true**, it means the button has just been **pressed**, and only then will the LED state be toggled. Afterward, the `lastButtonState` is updated to the current `buttonState` to remember the button’s new state for the next loop.

In summary, `lastButtonState` helps detect when the button is **newly pressed**, avoiding multiple toggles during a single press.

## Example: Changes from 0 to 1 and 1 to 0
### Initial States
- `buttonState` = 1 (Button is **unpressed**, assuming a pull-up resistor or internal pull-up configuration.)
- `lastButtonState`  = 1 (The button was previously **unpressed** as well.)
- `ledState` = 0 (LED is initially **OFF**.)

### Case 1: Button is Unpressed (Initial state)
- `buttonState` = 1 (Button is **unpressed**, **no change**.)
- `lastButtonState`  = 1 (From previous loop, still **unpressed**.)

Here, nothing happens because the button was **not pressed**, and the LED remains **off**. The **key condition** is not met, as there’s **no change** from `lastButtonState`  to `buttonState` (both 1).

### Case 2: Button is Pressed (First press)
Now the button is **pressed**:
- `buttonState` = 0 (Button is now **pressed**.)
- `lastButtonState` = 1 (From the previous loop, the button was **unpressed**.)

The condition:

```C
if (buttonState == 0 && lastButtonState == 1)
```

is **true** because the button was just **pressed** (changed from **unpressed** to **pressed**). As a result:

The `ledState` toggles: `ledState` = `!ledState`; (so `ledState` changes from 0 to 1, turning the LED **on**).

`lastButtonState` is updated to `buttonState` (`lastButtonState` = 0) to remember that the button is currently **pressed**.
LED is **ON** now.

### Case 3: Button is Still Pressed (Holding the button down)
- `buttonState` = 0 (Button is still **pressed**.)
- `lastButtonState` = 0 (From the previous loop, the button is still **pressed**.)

Here, the condition:

```C
if (buttonState == 0 && lastButtonState == 1)
```

is **false** because both `buttonState` and `lastButtonState` are 0 (the button was already **pressed** in the last loop). So the `ledState` does not toggle again.

LED stays **ON** because nothing changes while the button is held down.

### Case 4: Button is Released
Now the button is released:
- `buttonState` = 1 (Button is **unpressed**.)
- `lastButtonState` = 0 (From the previous loop, the button was **pressed**.)

Again, the condition:

```C
if (`buttonState` == 0 && lastButtonState == 1)
```

is **false**, since `buttonState` = 1 and `lastButtonState` = 0. The LED state remains **unchanged**.

LED is still **ON** because the condition isn’t met when the button is released.

### Case 5: Button is Pressed Again (Second press)
- `buttonState` = 0 (Button is **pressed** again.)
- `lastButtonState` = 1 (From the previous loop, the button was **unpressed**.)

Now, the condition:

```C
if (`buttonState` == 0 && lastButtonState == 1)
```

is **true** again because the button is **pressed** after being **unpressed**. As a result:

The `ledState` toggles: `ledState` = `!ledState`; (so `ledState` changes from 1 to 0, turning the LED **off**).

`lastButtonState` is updated to `buttonState` (`lastButtonState` = 0) to remember the button is currently **pressed**.
LED is **OFF** now.

## Summary of Changes:
**Unpressed to Pressed (1 to 0)**
- **Condition met**: The LED toggles (**ON** or **OFF**).

**Pressed to Pressed (0 to 0) (holding the button down)**
- **Condition not met**: LED stays the same.

**Pressed to Unpressed (0 to 1)**
- **Condition not met**: LED stays the same.

**Unpressed to Pressed again (1 to 0)**
- **Condition met**: The LED toggles again.

By using `lastButtonState`, you ensure that the LED toggles only once when the button is **pressed**, and no further toggles occur while the button is held down.

