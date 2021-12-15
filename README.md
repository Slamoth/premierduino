# premierdunio
Arduino Adobe Premier USB JogWheel Controller

- 1 Click: Turn fast forwared and rewind ON/OFF
- 2 Clicks: Selects IN position
- Second 2 Clicks: Selects OUT position
- 3 Clicks: Reset IN/OUT points

# Code
```
#include <Keyboard.h>
#define inputCLK 4
#define inputDT 5
#define inputSW 6

char ctrlKey = KEY_LEFT_GUI;

int currentStateCLK;
int previousStateCLK; 

int functionFast = 0;        // fast hotkey
int functionInout = 0;       // in/out hotkey

int buttonPushCounter = 0;   // counter for the number of button presses
int buttonState = 0;         // current state of the button
int lastButtonState = 0;     // previous state of the button
long lastClickTime = 0;

int buttonClickInterval = 500;  // button click count check interval

String keysToSend = "";

void setup() {
  // Set encoder pins as inputs  
  pinMode (inputCLK,INPUT);
  pinMode (inputDT,INPUT);
  pinMode(inputSW, INPUT_PULLUP);
  Serial.begin (115200);

  previousStateCLK = digitalRead(inputCLK);
  Keyboard.begin();
}

void loop() {

  // jog wheel codes
  currentStateCLK = digitalRead(inputCLK);
  if (currentStateCLK != previousStateCLK){ 
    if (functionFast == 1) {
      Keyboard.press(KEY_LEFT_SHIFT);
    }
    if (digitalRead(inputDT) != currentStateCLK) { 
      // CCW rotation
      Keyboard.press(KEY_LEFT_ARROW);
    } else {
      // CW rotation
      Keyboard.press(KEY_RIGHT_ARROW);
    }
  }
  previousStateCLK = currentStateCLK;

  // button click codes
  buttonState = digitalRead(inputSW);
  if (buttonState != lastButtonState) {
    if (buttonState == LOW) {
      lastClickTime = millis();
      buttonPushCounter++;
      buttonState == LOW;
    } else {
      buttonState == HIGH;
    }
    delay(50);
  }

  if ( buttonPushCounter > 0 && (millis() - lastClickTime) >  buttonClickInterval) {
    Serial.print("Counter: " + String(buttonPushCounter) + " ");
    switch (buttonPushCounter) {
      case 1: // one click turns fast ON/OFF
        switch (functionFast) {
        case 0:
          // turn fast forward and rewind ON
          functionFast = 1;
          break;
        case 1:
          // turn fast forward and rewind OFF
          functionFast = 0;
          break;
        default:
          break;
        }
        break;
      case 2: // two clicks turn INOUT ON/OFF
        switch (functionInout) {
        case 0:
          // send in hotkey
          Keyboard.press('i');
          functionInout = 1;
          break;
        case 1:
          // send out hotkey
          Keyboard.press('o');
          functionInout = 0;
          break;
        default:
          break;
        }
        break;
      case 3: // clear INOUT
        Keyboard.press(KEY_LEFT_ALT);
        Keyboard.press('x');
        break;
    }
    buttonPushCounter = 0;
  }
  
  lastButtonState = buttonState;(inputSW);
  Keyboard.releaseAll();
}
```
