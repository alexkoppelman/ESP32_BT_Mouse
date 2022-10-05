# ESP32_BT_Mouse
Have an esp32 emulate a Bluetooth mouse

### This version has
- A switch to enable/disable movement
  Switch connected to pin 19 and ground
- A led to indicae its on/off (could use onboard but want to integrate it into something)
  led connected to pin 23 using a 200Ω resistor (called it onboard as using onboard pin (2?) was initial idea)

## Arduino code still WIP. need to:
- add bit more randomness
- set timer differently?
- 

## Libraries used:
- ezButton, since it handles a switch quite nicely too https://www.arduino.cc/reference/en/libraries/ezbutton/
- BleMouse https://github.com/T-vK/ESP32-BLE-Mouse
- NoDelay needed to non-blocking delay some things without using a while loop and have the switch ignored. https://www.arduino.cc/reference/en/libraries/nodelay/


```
// #include "Button2.h"
#include <ezButton.h>
#include <BleMouse.h>
#include <NoDelay.h>

/////////////////////////////////////////////////////////////////

// #define BUTTON_PIN  19
ezButton toggleSwitch(19);
#define ONBOARD_LED 23
int OnBoard_state = LOW;

/////////////////////////////////////////////////////////////////

bool mouseonoff = false;
bool mouseup = false;
long randNumber;
/////////////////////////////////////////////////////////////////

//Button2 button;

int randBattery = random(10,100);
BleMouse bleMouse("Mighty Mouse", "Mighty Mouse BLE Mouse Ca", randBattery);
/////////////////////////////////////////////////////////////////
noDelay mousePause(6000);
noDelay shortPause(1000);

void movemouse();
/////////////////////////////////////////////////////////////////

void setup() {
  Serial.begin(115200);
  Serial.println("Booting MightyMouse!");
  
  bleMouse.begin();
  
  // start button
  //button.begin(BUTTON_PIN);
  //button.setTapHandler(tap);
  toggleSwitch.setDebounceTime(50);
  
  // initialize LED 
  pinMode(ONBOARD_LED, OUTPUT);
}


/////////////////////////////////////////////////////////////////
/////////////////////////////////////////////////////////////////

void loop() {
  toggleSwitch.loop();
  
  mousePause.update();

  //button.loop();
  
  movemouse();
}

/////////////////////////////////////////////////////////////////

void movemouse(){
  int state = toggleSwitch.getState();

  if(state == LOW){
    Serial.println("switched on");
    digitalWrite(ONBOARD_LED, HIGH);
   
   if(bleMouse.isConnected() ) {
    //&& mouseonoff == true
   if(mousePause.update()) {
      Serial.println("start moving");
      unsigned long startTime;
      digitalWrite(ONBOARD_LED, HIGH);
      
      startTime = millis();
        while(millis()<startTime+2000) {
          bleMouse.move(0,-3);
          delay(100);
        }
      
      startTime = millis();
        while(millis()<startTime+2000) {
          bleMouse.move(0,3);
          delay(100);
        }
      
      Serial.println("Wait");
      }
      }
       else {
          digitalWrite(ONBOARD_LED, LOW);
          return; 
          
      }
  } else {
    digitalWrite(ONBOARD_LED, LOW);
    Serial.println("Switched Off");
    }
}

/////////////////////////////////////////////////////////////////
/*
void tap(Button2& btn) {
    Serial.println("pressed"); 
    OnBoard_state = !OnBoard_state;
    digitalWrite(ONBOARD_LED, OnBoard_state);
    mouseonoff = !mouseonoff;  
}*/
/////////////////////////////////////////////////////////////////
```
