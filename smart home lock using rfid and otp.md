#include <Arduino.h>
#include <ESP32Servo.h>
#include <SPI.h>
#include <MFRC522.h>

#define SS_PIN SDA
#define RST_PIN 27
#define LED_G 13 // Green LED pin
#define LED_R 15 // Red LED pin
#define BUZZER 2 // Buzzer pin

MFRC522 mfrc522(SS_PIN, RST_PIN); // Create MFRC522 instance
Servo myServo; // Define servo name

int generatedOTP = 0;
int greenLEDPin = 13;
int redLEDPin = 15;
int buzzerPin = 2;
int tries = 0;

void setup() {
  Serial.begin(115200);
  randomSeed(analogRead(0)); // Initialize random number generator
  pinMode(greenLEDPin, OUTPUT);
  pinMode(redLEDPin, OUTPUT);
  pinMode(buzzerPin, OUTPUT);
  myServo.attach(33); // Attaches the servo on pin 33

  SPI.begin(); // Initialize SPI bus
  mfrc522.PCD_Init(); // Initialize MFRC522
  pinMode(LED_G, OUTPUT);
  pinMode(LED_R, OUTPUT);
  pinMode(BUZZER, OUTPUT);
  noTone(BUZZER);
  Serial.println("Put your card to the reader...");
  Serial.println();

  generateOTP(); // Generate the initial OTP
}

void loop() {
  // Look for new cards
  if (mfrc522.PICC_IsNewCardPresent() && mfrc522.PICC_ReadCardSerial()) {
    // Show UID on serial monitor
    Serial.print("UID tag: ");
    String content = "";
    for (byte i = 0; i < mfrc522.uid.size; i++) {
      Serial.print(mfrc522.uid.uidByte[i] < 0x10 ? " 0" : " ");
      Serial.print(mfrc522.uid.uidByte[i], HEX);
      content.concat(String(mfrc522.uid.uidByte[i] < 0x10 ? " 0" : " "));
      content.concat(String(mfrc522.uid.uidByte[i], HEX));
    }
    Serial.println();
    Serial.print("Message: ");
    content.toUpperCase();

    // Check if the RFID card is authorized
    if (content.substring(1) == "96 2C 71 06") { // Change the UID of the authorized card
      Serial.println("Authorized access");
      Serial.println();
      delay(500);
      digitalWrite(LED_G, HIGH);
      tone(BUZZER, 500);
      delay(300);
      noTone(BUZZER);
      myServo.write(180);
      delay(5000);
      myServo.write(0);
      digitalWrite(LED_G, LOW);
    } else {
      Serial.println("Access denied");
      digitalWrite(LED_R, HIGH);
      tone(BUZZER, 300);
      delay(1000);
      digitalWrite(LED_R, LOW);
      noTone(BUZZER);
    }

    // Clear the UID read
    mfrc522.PICC_HaltA();
  }

  // Print the generated OTP to the Serial Monitor
  Serial.println("Generated OTP: " + String(generatedOTP));

  // Wait for user input
  Serial.println("Enter OTP:");
  while (!Serial.available()) {
    // Wait for user input
  }

  // Read user input
  String userOTPString = Serial.readString();
  int userOTP = userOTPString.toInt();

  // Verify OTP
  if (userOTP == generatedOTP) {
    Serial.println("OTP Verified!");

    // Rotate the servo to 180 degrees
    myServo.write(180);
    digitalWrite(greenLEDPin, HIGH);
    delay(5000); // Wait for 5 seconds

    // Return the servo to the initial position (e.g., 0 degrees)
    myServo.write(0);

    // Blink green LED
    digitalWrite(greenLEDPin, LOW);
    digitalWrite(redLEDPin, HIGH);

    // Generate a new OTP
    generateOTP();
  } else {
    Serial.println("OTP Verification Failed. Try Again.");

    // Beep the buzzer when access is denied
    digitalWrite(greenLEDPin, LOW);
    delay(500);
    digitalWrite(redLEDPin, HIGH);
    digitalWrite(buzzerPin, HIGH);
    delay(500);
    digitalWrite(buzzerPin, LOW);
    digitalWrite(redLEDPin, LOW);
    tries++;

    // Beep for a longer duration when access is denied after 3 tries
    if (tries == 3) {
      Serial.println("OTP invalid");
      digitalWrite(redLEDPin, HIGH);
      digitalWrite(buzzerPin, HIGH);
      delay(1000);
      digitalWrite(buzzerPin, LOW);
      digitalWrite(redLEDPin, LOW);
    }
  }
}

void generateOTP() {
  // Generate a new 6-digit OTP
  generatedOTP = 0;
  for (int i = 0; i < 6; i++) {
    generatedOTP = generatedOTP * 10 + random(0, 10); // Generate a random digit between 0 and 9
  }
}
