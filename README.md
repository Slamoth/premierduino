# premierduino
Adventures with an Arduino and an encoder !

- 1 Click: Start / Stop
- 2 Click: Turn fast forwared and rewind ON/OFF
- 3 Clicks: Selects IN position and Second 3 Clicks: Selects OUT position
- 4 Clicks: Reset IN/OUT points
- 5 Clicks -> set JogWheel for zoom in/out ON/OFF

# Code
```
#include <Keyboard.h>
#define inputCLK 4
#define inputDT 5
#define inputSW 6

char ctrlKey = KEY_LEFT_GUI;

int currentStateCLK;
int previousStateCLK; 


int jogFunction = 0; // JogWheel function selector
// 0: time
// 1: zoom

int functionStartStop = 0;        // Start / stop hotkey
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
//  Serial.begin (115200);

  previousStateCLK = digitalRead(inputCLK);
  Keyboard.begin();
}

void loop() {

  // jog wheel codes
  currentStateCLK = digitalRead(inputCLK);
  if (currentStateCLK != previousStateCLK) {
    if (functionFast == 1) {
      Keyboard.press(KEY_LEFT_SHIFT);
    }
    if (digitalRead(inputDT) != currentStateCLK) { 
      // CCW rotation
      if (jogFunction == 0) {
        Keyboard.press(KEY_LEFT_ARROW);
      } else {
        Keyboard.press('-');
      }
    } else {
      // CW rotation
      if (jogFunction == 0) {
        Keyboard.press(KEY_RIGHT_ARROW);
      } else {
        Keyboard.press('=');
      }
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
      case 1: // play / stop
        Keyboard.press(32); // press space
        break;
      case 2: // fast ON/OFF
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
      case 3: // INOUT ON/OFF
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
      case 4: // clear INOUT
        Keyboard.press(KEY_LEFT_ALT);
        Keyboard.press('x');
        functionInout = 0;
        break;
      case 5: // Zoom
        switch (jogFunction) {
        case 0:
          // turn fast forward and rewind ON
          jogFunction = 1;
          break;
        case 1:
          // turn fast forward and rewind OFF
          jogFunction = 0;
          break;
        default:
          break;
        }
        break;
    }
    buttonPushCounter = 0;
  }
  
  lastButtonState = buttonState;(inputSW);
  Keyboard.releaseAll();
}
```
