# 🌱 Arduino Soil Moisture Sensor Project

A simple *Smart Soil Monitoring System* built using *Arduino UNO, Soil Moisture Sensor, I2C LCD, and a Buzzer*.  
This project measures soil moisture levels and displays them on an LCD. If the soil is too dry, an alert is triggered with a buzzer.

---

## 📷 Project Overview

### Circuit Diagram
![Circuit Diagram](./IMG-20250828-WA0003.jpg)

### Fritzing Connections
![Connections](./Screenshot%202025-08-28%20220455.png)

---

## 🛠 Components Used
- Arduino UNO  
- Soil Moisture Sensor V2.0  
- I2C LCD Display (16x2)  
- Active Buzzer  
- Breadboard & Jumper Wires  
- USB Cable for Arduino  

---

## 🔌 Connections (Arduino UNO)

### Soil Moisture Sensor → UNO
- VCC → 5V  
- GND → GND  
- AOUT → A0  

### I2C LCD → UNO
- VCC → 5V  
- GND → GND  
- SDA → A4  
- SCL → A5  

### Buzzer → UNO
- + → D8  
- - → GND  

---

## 💻 Code

```cpp
#include <Wire.h>
#include <LiquidCrystal_I2C.h>

LiquidCrystal_I2C lcd(0x27, 16, 2); // LCD I2C address 0x27 (sometimes 0x3F)

const int soilPin = A0;      // Soil sensor analog output connected to A0
const int buzzerPin = 8;     // Buzzer connected to digital pin 8

int soilValue = 0;

void setup() {
  Serial.begin(9600);

  // LCD setup
  lcd.init();
  lcd.backlight();
  lcd.setCursor(0, 0);
  lcd.print("Soil Monitor");

  // Buzzer setup
  pinMode(buzzerPin, OUTPUT);
  digitalWrite(buzzerPin, LOW);
}

void loop() {
  // Read soil sensor
  soilValue = analogRead(soilPin);

  // Convert raw value to percentage (0 = dry, 100 = wet)
  int moisturePercent = map(soilValue, 1023, 300, 0, 100); 
  moisturePercent = constrain(moisturePercent, 0, 100);

  // Display on LCD
  lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print("Moisture:");
  lcd.print(moisturePercent);
  lcd.print("%");

  // Print on Serial Monitor
  Serial.print("Soil Value: ");
  Serial.print(soilValue);
  Serial.print("  Moisture: ");
  Serial.print(moisturePercent);
  Serial.println("%");

  // Buzzer alert if dry
  if (moisturePercent < 30) {
    lcd.setCursor(0, 1);
    lcd.print("Soil is Dry!");
    digitalWrite(buzzerPin, HIGH);
    delay(500);
    digitalWrite(buzzerPin, LOW);
    delay(500);
  } else {
    lcd.setCursor(0, 1);
    lcd.print("Soil OK :)   ");
    digitalWrite(buzzerPin, LOW);
    delay(1000);
  }
}
