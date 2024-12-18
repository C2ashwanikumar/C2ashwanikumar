# SMART HOME LOCK USING RFID AND OTP USING BHARAT PI

## Project Overview
This project transforms a traditional door lock into a smart security system. It combines RFID cards and OTPs (One-Time Passwords) to provide dual-layer authentication. Access to the door is granted either by scanning an RFID card or by entering a correct OTP, making it highly secure and convenient.

## Components Required
1. **Bharat Pi (Board):** A small, powerful board combining the ESP32 microcontroller with a 4G/LTE SimCom A7672S module, ideal for IoT projects.
2. **Servo Motor:** Used to control the lock mechanism, it rotates the lock to unlock or lock the door.
3. **RFID Reader:** Detects RFID cards for authorized access.
4. **LEDs:** Green (for success) and Red (for failure) to visually indicate the status of authentication.
5. **Jumper Wires:** Used to make necessary connections between components.
6. **Buzzer Module:** Provides sound alerts for successful or failed authentication.

## Software Requirements
1. **Arduino IDE:** The primary software to program the Bharat Pi board.
2. **CH341 Serial Drivers:** For communication between the Bharat Pi and the computer.

## Key Features
1. **Dual Authentication:** Combines RFID card and OTP verification for enhanced security.
2. **RFID Convenience:** Easy access by swiping or tapping the RFID card.
3. **OTP Security:** Time-sensitive OTPs add an extra layer of protection.
4. **Auto-Locking:** The system automatically locks the door after access.
5. **Customized Access:** Specific permissions can be assigned to users.

## Components Explanation
1. **Bharat Pi (Board):** This board integrates the ESP32 microcontroller with a 4G/LTE module, enabling communication for IoT applications.
2. **Servo Motor:** A precise mechanism that moves to lock or unlock the door.
3. **RFID Reader:** This detects authorized RFID cards and triggers the unlocking process. Unauthorized cards will not be allowed access.
4. **LEDs:**
   - **Green LED:** Indicates successful access.
   - **Red LED:** Indicates access denial.
5. **Jumper Wires & Buzzer Module:** Jumper wires make connections, while the buzzer alerts users of the access status.

## Circuit Connections
### RFID:
- **SS pin** -> GPIO5
- **SCK pin** -> GPIO18
- **MOSI pin** -> GPIO23
- **MISO pin** -> GPIO19
- **RST pin** -> GPIO27

### Servo Motor:
- Connected to GPIO33.

### LEDs:
- **Green LED** -> GPIO13
- **Red LED** -> GPIO15

### Buzzer:
- Connected to GPIO2.

## Working of the System
### RFID Authentication
- When the user presses 'a' on the serial monitor, the system detects the RFID card.
- If the RFID card is authorized, the system will rotate the servo motor, unlocking the door and lighting up the Green LED.
- Unauthorized RFID cards will not trigger the servo, and the Red LED will light up.

### OTP Authentication
- The user presses 'A' on the serial monitor, generating a random OTP.
- The system waits for the user to enter the OTP. If it matches the generated one, the Green LED lights up, and the servo motor unlocks the door.
- If the OTP is incorrect, the Red LED lights up, and the buzzer emits a failure beep.

## Video Demo
Watch the video demo here: [Smart Home Lock Video](https://youtu.be/TDK9QmLXcGk?si=znMA4nDfshcZkOGa)

## Additional Notes
- **Security:** The system provides dual-layer security (RFID and OTP) for added protection.
- **Flexibility:** Users can customize the access permissions, making it adaptable for various scenarios.
- **IoT Integration:** With Bharat Pi’s 4G/LTE module, this system can be expanded for remote monitoring and control.

This project can be further expanded by adding features like mobile app integration for OTP generation or remote unlocking.
