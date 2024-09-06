# Math Quiz

This project implements a Math Quiz game using an LCD 16x2 display. The game presents arithmetic problems involving addition, subtraction, multiplication, and division. The player must answer correctly within a time limit.

## Components Used

- Arduino Uno
- 16x2 LCD Display
- Two Push Buttons (for True/False answers)
- Jumper Wires
- Breadboard

## Pin Configuration

- **LCD Pins:**

  - **RS**: Pin 2
  - **E**: Pin 3
  - **D4**: Pin 4
  - **D5**: Pin 5
  - **D6**: Pin 6
  - **D7**: Pin 7

- **Button Pins:**
  - **False Button**: Pin 10
  - **True Button**: Pin 11

## Game Configuration

- **Time Limit**: 6 seconds per question
- **Max Number for Questions**: 3000

## Symbols

The LCD displays a custom symbol representing a timer (hourglass).

## Code Overview

```cpp
#include <LiquidCrystal.h>

const int PIN_RS = 2;
const int PIN_E  = 3;
const int PIN_D4 = 4;
const int PIN_D5 = 5;
const int PIN_D6 = 6;
const int PIN_D7 = 7;

const int BTN_FALSE = 10;
const int BTN_TRUE  = 11;

LiquidCrystal lcd(PIN_RS, PIN_E, PIN_D4, PIN_D5, PIN_D6, PIN_D7);

// Constants
const int MODE_PERTAMBAHAN  = 0;
const int MODE_PENGURANGAN  = 1;
const char MODES_SYMBOLS[]  = { '+', '-' };

byte time_symbol[8] = {
    0b11111,
    0b01110,
    0b01110,
    0b00100,
    0b00100,
    0b01110,
    0b01110,
    0b11111
};

// Game Configurations
const int GAME_TIMER    = 6;    // 6 seconds to answer
const int ANGKA_MAX     = 3000;

// Game Variables
bool jawaban;

void setup() {
    randomSeed(analogRead(A0));
    pinMode(BTN_FALSE, INPUT_PULLUP);
    pinMode(BTN_TRUE, INPUT_PULLUP);
    lcd.createChar(0, time_symbol);
    lcd.begin(16, 2);
}

void loop() {
    lcd.clear();
    lcd.print("Math Quiz");

    long waktu = millis() + GAME_TIMER * 1000;
    generate_soal();

    while (digitalRead(BTN_TRUE) == HIGH &&
           digitalRead(BTN_FALSE) == HIGH &&
           waktu > millis())
    {
        refresh_display_time(waktu);
    }

    lcd.setCursor(0, 1);
    if (waktu < millis()) {
        lcd.print("> WAKTU HABIS!!!");
    } else if (digitalRead(BTN_TRUE) == LOW && jawaban) {
        lcd.print("> Nice :)       ");
    } else if (digitalRead(BTN_FALSE) == LOW && !jawaban) {
        lcd.print("> Nice :)       ");
    } else {
        lcd.print("> Coba lagi :(  ");
    }

    delay(3000);
}

void refresh_display_time(long waktu) {
    lcd.setCursor(12, 0);
    lcd.write((byte)0); // Custom symbol "Hourglass"
    lcd.print(":");
    lcd.print((waktu - millis()) / 1000);
}

void generate_soal() {
    int angka1, angka2;
    int jawaban_val;

    angka1 = random(ANGKA_MAX);
    angka2 = random(ANGKA_MAX);

    lcd.setCursor(0, 1);
    lcd.print(angka1);

    switch (random(2)) {
    case MODE_PERTAMBAHAN:
        jawaban_val = angka1 + angka2;
        lcd.print(MODES_SYMBOLS[MODE_PERTAMBAHAN]);
        break;

    case MODE_PENGURANGAN:
        jawaban_val = angka1 - angka2;
        lcd.print(MODES_SYMBOLS[MODE_PENGURANGAN]);
        break;
    }

    lcd.print(angka2);
    lcd.print(" = ");

    if (random(2) == 0) { // Correct or Incorrect answer
        jawaban = true;
        lcd.print(jawaban_val);
    } else {
        jawaban = false;
        lcd.print(jawaban_val + random(-10, 10));
    }
}
```

## How to Use

1. Connect the LCD and push buttons to the specified pins on the Arduino.
2. Upload the code to your Arduino.
3. Start the quiz. The LCD will display a math problem.
4. Answer the question by pressing the True or False button within the time limit.
5. Check the result displayed on the LCD after the time expires.

## Notes

- Make sure all connections are secure to avoid any issues.
- The game timer and question generation can be adjusted by modifying the GAME_TIMER and ANGKA_MAX constants.
