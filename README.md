#  RFID Based Attendance System using 8051 Microcontroller

![Assembly](https://img.shields.io/badge/Language-8051%20Assembly-blue.svg) ![Hardware](https://img.shields.io/badge/Platform-8051%20MCU-lightgrey.svg)

A simple yet effective RFID-based attendance system implemented in 8051 Assembly language. This project is designed to identify students or employees by their unique RFID cards and mark their attendance on a 16x2 LCD screen.



---

## 📋 Features

* **Real-time Attendance**: Marks attendance instantly upon scanning a valid RFID card.
* **LCD Interface**: Displays user information, status ("PRESENT"), and error messages on a 16x2 character LCD.
* **User Verification**: Supports a hardcoded list of authorized users and rejects any unauthorized cards.
* **Simple and Lightweight**: Written entirely in 8051 Assembly, making it highly efficient and suitable for resource-constrained microcontrollers.

---

## 🛠️ Hardware & Software Requirements

### Hardware
* **Microcontroller**: An 8051-based MCU (e.g., AT89S52 or AT89C51).
* **Display**: A standard 16x2 Character LCD Display.
* **RFID Reader**: A TTL Serial RFID Reader Module (e.g., EM-18).
* **RFID Cards**: 12-byte RFID cards or tags.
* **Power Supply**: A stable 5V power supply for the circuit.
* **Connecting Wires** and a breadboard/PCB.

### Software
* **IDE**: Keil µVision or any other IDE/assembler compatible with the 8051 architecture.
* **Programmer**: An 8051 programmer/flasher to upload the hex code to the microcontroller.

---

## ⚙️ How It Works

The system's logic is straightforward and operates in a continuous loop.

1.  **Initialization**:
    * The 8051's **Timer 1** is configured in Mode 2 (8-bit auto-reload) to set the baud rate for serial communication (9600 bps with a standard 11.0592 MHz crystal).
    * The Serial Control Register (**SCON**) is configured for 8-bit data, 1 stop bit, and receive enabled.
    * The 16x2 LCD is initialized in 8-bit mode.

2.  **Welcome Screen**:
    * Upon startup, the LCD displays a welcome message like "RFID READER" and "Swipe Card...".

3.  **RFID Reading**:
    * The microcontroller waits for data by polling the **Receive Interrupt (`RI`) flag**.
    * When an RFID card is scanned, the reader sends its unique 12-byte ID to the 8051's **SBUF** register, one byte at a time.
    * The code reads these 12 bytes and stores them in internal RAM, starting at memory location **20H**.

4.  **Verification and Display**:
    * The received 12-byte ID is compared, byte-by-byte, against a list of pre-authorized IDs hardcoded into the program.
    * **If a match is found**: The system displays the corresponding student's name/ID (e.g., `SUJAL(22BEC1209)`) and the status `PRESENT`.
    * **If no match is found**: An `UNAUTHORISED...` error message is displayed.

5.  **Loop**: After a short delay, the system resets and waits for the next card scan.

---

## 🚀 Setup and Installation

1.  **Hardware Connections**:
    * Connect the **16x2 LCD** to the 8051. In this code, **Port 2 (P2)** is the data bus. The control pins (**RS, RW, E**) are connected to **P3.7, P3.6, and P3.5**.
    * Connect the **TX pin** of the RFID Reader to the **RXD pin (P3.0)** of the 8051.
    * Power up the entire circuit with a 5V supply.

2.  **Compiling and Flashing**:
    * Open the `.asm` file in your 8051 IDE (e.g., Keil µVision).
    * Assemble the code to generate the `.hex` file.
    * Use an 8051 programmer to flash the `.hex` file onto your microcontroller.

---

## ✏️ Customization: Adding Your Own RFID Cards

To add your own students and RFID cards, you need to modify the assembly code.

1.  **Find Your Card's ID**:
    * Connect your RFID reader to a PC via a USB-to-TTL converter and read the 12-byte ID using a serial monitor (like the one in the Arduino IDE).

2.  **Modify the Code**:
    * Navigate to the verification section of the code (e.g., the `PERSON2` label). Change the character values in the `CJNE` instructions to match your card's ID.

    ```assembly
    PERSON2:
        MOV R0, #20H
        CJNE @R0, #"Y", PERSON3 ; <-- Change "B" to your ID's 1st character
        INC R0
        CJNE @R0, #"O", PERSON3 ; <-- Change "A" to your ID's 2nd character
        INC R0
        ; ...and so on for all 12 bytes
    ```
    * Update the student information in the data section at the end of the file.
    ```assembly
    STUDENT2: DB 'YOUR_NAME(ID)',0 ; <-- Change the name and ID here
    ```
    * Re-assemble the code and flash it to your 8051. You can add more `PERSON` blocks to support more users.
