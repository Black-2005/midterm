# ນ້ອງຈະມາເຮັດໂປຮເຈັກໃນຫົວຂໍ້ Reaction Game ໃນ Tinkercad
ໂດຍໃນໂປຮເຈັກນີ້ນ້ອງຈະສ້າງເກມຂຶ້ນມາໂດຍມີຊື່ວ່າ Reaction Game ກະຄືເກມຝືກການຕອຍສະຫນອງໂດຍນ້ອງຈະເອົາ LED RGB ມາສອງອັນໂດຍກ່ອນຜູ້ຫລີ້ນຈະເລິ່ມເກມຈະມີຂໍ້ຄວາມນີ້ຂຶ້ນ Waiting for button press to start... ໃຫ້ກົດປຸ້ມເພື່ອເລີ່ມເກມຫລັງຈາກນັ້ນດອກໄຟຈະສຸ້ມສີໄປເລື່ອຍໆຈົນກວ່າຜູ້ຫລີ້ນຈະກົດປຸ້ມບໍ່ທັນຖ້າກົດທັນຈະໃຫ້ Buzzer ສົ່ງສຽງອອກມາ 500hz ແລະ ສະແດງຂໍ້ຄວາມ You respond in time! ຖ້າກົດບໍ່ທັນຈະໃຫ້ສົ່ງສຽງອອກມາ 1000hz ແລະ ສະແດງຂໍ້ຄວາມToo slow! Press the button to restart. ຫລັງຈາກນັ້ນຈະໃຫ້ໄຟປິດແລ້ວມາເລິ່ມຫລີ້ນໃຫມ່ຈະມີຂໍ້ຄວາມບອກວ່າ Waiting for button press to start...
#### ອຸປະກອນ
| Name  | Quantity | Component      |
| ----- | -------- | -------------- |
| U1    | 1        | Arduino Uno R3 |
| B1    | 1        | BreadBoard     |
| D1-D2 | 2        | RCBG LED RGB   |
| R1-R7 | 7        | 330 Ω Resistor |
| S1    | 1        | Pushbutton     |
| PIZ01 | 1        | Piezo          |
1. Arduino Uno R3
![[Pasted image 20250202030746.png]]
2. BreadBoard
![[Pasted image 20250202030831.png]]
1. RCBG LED RGB
![[Pasted image 20250203003131.png]]
2. 330 Ω Resistor
![[Pasted image 20250203012847.png]]
3. Pushbutton
![[Pasted image 20250203001055.png]]
4. Piezo
![[Pasted image 20250203010015.png]]
#### ການເຊື່ອມວົງຈອນ
1. LED RGB (3 ຂາສີແຍກ)
RGB LEDs ມີສາມ pins ສັນຍານ (ສີແດງ, ສີຂຽວ, ສີຟ້າ) ແລະ pin ກາງ (GND).

ຂາສີແດງ (LED ສີແດງ) →ເຊື່ອມຕໍ່ກັບ pin 9 ຂອງ Arduino ຜ່ານ resistor (220Ω).
ຂາສີຂຽວ (LED ສີຂຽວ) → ເຊື່ອມຕໍ່ກັບ pin 10 ຂອງ Arduino ຜ່ານ resistor (220Ω).
ໄຟ LED ສີຟ້າ → ເຊື່ອມຕໍ່ກັບ pin 11 ຂອງ Arduino ຜ່ານຕົວຕ້ານທານ (220Ω).
ຂາ GND (ຂາກາງຂອງ RGB LED) →ເຊື່ອມຕໍ່ GND ຂອງ Arduino.
1. Buzzer (ເຄື່ອງສ້າງສຽງ)
Buzzer positive terminal (+) → ເຊື່ອມຕໍ່ຫາ pin 3 ຂອງ Arduino.
Buzzer negative terminal (-) → ເຊື່ອມຕໍ່ຫາ GND ຂອງ Arduino.
3. ປຸ່ມກົດ (Push Switch)
ຂາດຽວຂອງປຸ່ມກົດ → ເຊື່ອມຕໍ່ກັບ pin 2 ຂອງ Arduino.
ຂາອື່ນໆຂອງປຸ່ມກົດ→ເຊື່ອມຕໍ່ GND ຜ່ານຕົວຕ້ານທານດຶງລົງ (10kΩ).
ຂາດຽວກັນກັບ Pull-down resistor → ເຊື່ອມຕໍ່ກັບ 5V ເພື່ອໃຫ້ແຮງດັນໃນເວລາທີ່ກົດປຸ່ມ.
![[Pasted image 20250203013059.png]]
![[Pasted image 20250203013114.png]]
#### code
```c++
#define RED_PIN_1 9
#define GREEN_PIN_1 10
#define BLUE_PIN_1 11

#define RED_PIN_2 3
#define GREEN_PIN_2 5
#define BLUE_PIN_2 6

#define BUTTON_PIN 2
#define BUZZER_PIN 8

unsigned long startTime;
unsigned long reactionTimeLimit = 2000; 
int currentColor;

void setup() {
  pinMode(RED_PIN_1, OUTPUT);
  pinMode(GREEN_PIN_1, OUTPUT);
  pinMode(BLUE_PIN_1, OUTPUT);

  pinMode(RED_PIN_2, OUTPUT);
  pinMode(GREEN_PIN_2, OUTPUT);
  pinMode(BLUE_PIN_2, OUTPUT);

  pinMode(BUTTON_PIN, INPUT_PULLUP);
  pinMode(BUZZER_PIN, OUTPUT);

  Serial.begin(9600);
  randomSeed(analogRead(0));

  waitForRestart();  
}

void loop() {
  randomizeColor();
  startTime = millis();

  while (millis() - startTime < reactionTimeLimit) {
    if (digitalRead(BUTTON_PIN) == LOW) {
      correctReaction();
      delay(1000);
      return;
    }
  }

  missedReaction();
  waitForRestart();  
}

void randomizeColor() {
  currentColor = random(6);
  setColor(currentColor, RED_PIN_1, GREEN_PIN_1, BLUE_PIN_1);
  setColor(currentColor, RED_PIN_2, GREEN_PIN_2, BLUE_PIN_2);
}

void setColor(int color, int redPin, int greenPin, int bluePin) {
  if (color == -1) {
    analogWrite(redPin, 0);
    analogWrite(greenPin, 0);
    analogWrite(bluePin, 0);
    return;
  }

  switch (color) {
    case 0: analogWrite(redPin, 255); analogWrite(greenPin, 0); analogWrite(bluePin, 0); break;
    case 1: analogWrite(redPin, 0); analogWrite(greenPin, 255); analogWrite(bluePin, 0); break;
    case 2: analogWrite(redPin, 0); analogWrite(greenPin, 0); analogWrite(bluePin, 255); break;
    case 3: analogWrite(redPin, 255); analogWrite(greenPin, 255); analogWrite(bluePin, 0); break;
    case 4: analogWrite(redPin, 255); analogWrite(greenPin, 0); analogWrite(bluePin, 255); break;
    case 5: analogWrite(redPin, 0); analogWrite(greenPin, 255); analogWrite(bluePin, 255); break;
  }
}

void correctReaction() {
  tone(BUZZER_PIN, 1000, 200);
  Serial.println("You respond in time!");
}

void missedReaction() {
  
  setColor(-1, RED_PIN_1, GREEN_PIN_1, BLUE_PIN_1);
  setColor(-1, RED_PIN_2, GREEN_PIN_2, BLUE_PIN_2);
  
  tone(BUZZER_PIN, 500, 1000);
  Serial.println("Too slow! Press the button to restart.");
}


void waitForRestart() {

  setColor(-1, RED_PIN_1, GREEN_PIN_1, BLUE_PIN_1);
  setColor(-1, RED_PIN_2, GREEN_PIN_2, BLUE_PIN_2);

  Serial.println("Waiting for button press to start...");
  
  while (digitalRead(BUTTON_PIN) == HIGH) {
 
  }

  delay(500);  
}

```
![[code miniPJ.png]]
#### ຜົມລັບ
ຕອນຍັງບໍ່ທັນໄດ້ເລີ່ມເກມ
![[Pasted image 20250203013500.png]]
ຕອນເລິ່ມເກມ
![[Pasted image 20250203013523.png]]
ຕອນກົດປຸ້ມທັນ
![[Pasted image 20250203013645.png]]
ຕອນກົດປຸ່ມບໍ່ທັນ
![[Pasted image 20250203013716.png]]
#### ນີ້ຄື Tinkercad ຂອງນ້ອງທີ່ທົດລອງ
[Tinkercad ຂອງນ້ອງເອງ Reaction Game](https://www.tinkercad.com/things/3iIyaFsEGkO-reaction-game-?sharecode=HSn1FuRqAYxAm-tuOY5fiAjTdSFpxeu_QW4PflaqiX8)
![[Pasted image 20250203014134.png]]
