
# RFID-Based Door Lock System 🔒  
An Arduino-based security system using an **RFID module** to authenticate users and unlock a **servo motor-controlled door**. Unauthorized access triggers a **buzzer alert**.

## 📌 Features
✅ RFID authentication for secure access  
✅ Servo motor-controlled locking mechanism  
✅ Buzzer alert for unauthorized attempts  
✅ Expandable for multiple authorized cards  

## 🛠️ Hardware Used
- Arduino Uno/Nano  
- RC522 RFID Module  
- Servo Motor  
- Buzzer & LED  
- LCD Display (optional)  

## 📝 Code Example
```cpp
#include <SPI.h>
#include <MFRC522.h>
#include <Servo.h>

#define SS_PIN 10
#define RST_PIN 9
MFRC522 rfid(SS_PIN, RST_PIN);
Servo myServo;
String authorizedUID = "12AB34CD"; 

void setup() {
    Serial.begin(9600);
    SPI.begin();
    rfid.PCD_Init();
    myServo.attach(3);
    myServo.write(0);
    pinMode(7, OUTPUT);
}

void loop() {
    if (!rfid.PICC_IsNewCardPresent() || !rfid.PICC_ReadCardSerial()) {
        return;
    }
    String cardID = "";
    for (byte i = 0; i < rfid.uid.size; i++) {
        cardID += String(rfid.uid.uidByte[i], HEX);
    }
    Serial.println("Card ID: " + cardID);

    if (cardID == authorizedUID) {
        Serial.println("Access Granted! Unlocking...");
        myServo.write(90);
        digitalWrite(7, LOW);
        delay(3000);
        myServo.write(0);
    } else {
        Serial.println("Access Denied!");
        digitalWrite(7, HIGH);
        delay(1000);
        digitalWrite(7, LOW);
    }
    rfid.PICC_HaltA();
    rfid.PCD_StopCrypto1();
}
