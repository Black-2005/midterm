# ນ້ອງຈະມາເຮັດໂປຮເຈັກໃນຫົວຂໍ້ Light Level Control System ໃນ Tinkercad
ນ້ອງຈະເຮັດໂປຮເຈັກ Light Level Control System ໃນ Tinkercad ໂດຍຈະເປັນການຈຳລອງການປັບລະດັບຄວາມແຮງຂອງແດງໂດຍໃຊ້ Potentimeter ເປັນຕົວປັບຫລືຕົວຕ້ານເຊິ່ງນ້ອງເຮັດໃຫ້ລະດັບແສງໄຟມີຢູ່ 3 ລະດັບຄືແດງ,ຂຽວ,ຟ້າໂດຍແຕ່ລະສີຈະສະແດງເຖິງຄວາມແຮງຂອງແສງໂດຍຈະມີ Buzzer ເຂົ້າມາເປັນຕົວຊ່ວຍເພື່ອເຕືອນວ່າລະດັບຄວາມແຮງຂອງແສງມັນຢູ່ລະດັບໃດ.
#### ອຸປະກອນ
| Name   | Quantity | Component                 |
| ------ | -------- | ------------------------- |
| U1     | 1        | Arduino Uno R3            |
| B1     | 1        | BreadBoard                |
| D1     | 1        | RCBG LED RGB              |
| R1-R3  | 3        | 220 Ω Resistor            |
| S1     | 1        | Pushbutton                |
| PIZ01  | 1        | Piezo                     |
| Rpot1  | 1        | 10 kΩ Potentiometer       |
| Digit1 | 1        | Cathode 7 Segment Display |
1. Arduino Uno R3
![[Pasted image 20250202030746.png]]
2. BreadBoard
![[Pasted image 20250202030831.png]]
3. RCBG LED RGB
![[Pasted image 20250203003131.png]]
 4. 220 Ω Resistor
![[Pasted image 20250202031003.png]]
5. Pushbutton
![[Pasted image 20250203001055.png]]
6. Piezo
![[Pasted image 20250203010015.png]]
7. 10 kΩ Potentiometer
![[Pasted image 20250203015100.png]]
8. Cathode 7 Segment Display
![[Pasted image 20250203035434.png]]
#### ການເຊື່ອມວົງຈອນ
1. 7-Segment Display (Common Cathode) ການເຊື່ອມຕໍ່
ໃຊ້ pins 2 - 8 ຂອງ Arduino ເພື່ອເຊື່ອມຕໍ່ກັບ pins A-G ຂອງການສະແດງ 7-segment ຕາມລໍາດັບ.
ຂາ A → Arduino ຂາ 2
ຂາ B → Arduino ຂາ 3
ຂາ C → Arduino ຂາ 4
ຂາ D → Arduino ຂາ 5
ຂາ E → Arduino ຂາ 6
ຂາ F → Arduino ຂາ 7
ຂາ G → Arduino ຂາ 8
Common Cathode → GND (ຖ້າ Anode ທົ່ວໄປ, ເຊື່ອມຕໍ່ກັບ 5V)
2. ການເຊື່ອມຕໍ່ Potentiometer (ຕົວຕ້ານທານຕົວແປ)
Potentiometer ມີ 3 ຂາ, ເຊື່ອມຕໍ່ພວກເຂົາດັ່ງຕໍ່ໄປນີ້:
ຂາຊ້າຍ → GND
ຂາກາງ → ຂາ Arduino A0 (ເພື່ອອ່ານຄ່າຄວາມຕ້ານທານ)
ຂາຂວາ → 5V
3. ການເຊື່ອມຕໍ່ RGB LED (Cathode ທົ່ວໄປ)
RGB LED ມີ 4 ຂາ, ເຊື່ອມຕໍ່ກັບ Arduino ດັ່ງຕໍ່ໄປນີ້:
ຂາ R (ສີແດງ) → ຂາ Arduino 10
ຂາ G (ສີຂຽວ) → ຂາ Arduino 11
ຂາ B (ສີຟ້າ) → ຂາ Arduino 12
Common Cathode → GND (ຖ້າ Anode ທົ່ວໄປ, ໃຫ້ເຊື່ອມຕໍ່ 5V ແທນ)
4. ການເຊື່ອມຕໍ່ Buzzer
Buzzer (+) leg → Arduino pin 13
Buzzer (-) ຂາ → GND
5. ການເຊື່ອມຕໍ່ປຸ່ມ Button
ຂາດຽວຂອງປຸ່ມ → Arduino pin 9
ຂາອື່ນຂອງປຸ່ມ → GND
ໃຊ້ຕົວຕ້ານທານ 10KΩ ເປັນຕົວດຶງລະຫວ່າງ pin 9 → 5V ເພື່ອຮັບປະກັນການເຮັດວຽກທີ່ເຫມາະສົມຂອງປຸ່ມ.
![[Pasted image 20250203041830.png]]
![[Pasted image 20250203041845.png]]
![[Pasted image 20250203041918.png]]
#### code
```c++
int segPins[] = {2, 3, 4, 5, 6, 7, 8}; // ຂາ A-G ສໍາລັບ 7-segment display
#define POT_PIN A0          // Potentiometer
#define RGB_R 10            // RGB ສີແດງ
#define RGB_G 11            // RGB ສີຂຽວ
#define RGB_B 12            // RGB ສີນໍ້າເງິນ
#define BUZZER_PIN 13       // Buzzer ຂາ 13
#define RESET_BUTTON 9      // ປຸ່ມ Reset

int segmentDigits[10][7] = {
  {1, 1, 1, 1, 1, 1, 0}, // 0
  {0, 1, 1, 0, 0, 0, 0}, // 1
  {1, 1, 0, 1, 1, 0, 1}, // 2
  {1, 1, 1, 1, 0, 0, 1}, // 3
  {0, 1, 1, 0, 0, 1, 1}, // 4
  {1, 0, 1, 1, 0, 1, 1}, // 5
  {1, 0, 1, 1, 1, 1, 1}, // 6
  {1, 1, 1, 0, 0, 0, 0}, // 7
  {1, 1, 1, 1, 1, 1, 1}, // 8
  {1, 1, 1, 1, 0, 1, 1}  // 9
};

void setup() {
  Serial.begin(9600); // เริ่มต้นการใช้งาน Serial Communication
  pinMode(POT_PIN, INPUT); // ກຳນົດໃຫ້ Potentiometer ເປັນຂາ INPUT
  pinMode(RGB_R, OUTPUT);  // ກຳນົດໃຫ້ RGB ສີແດງ ເປັນ OUTPUT
  pinMode(RGB_G, OUTPUT);  // ສີຂຽວ
  pinMode(RGB_B, OUTPUT);  // ສີນໍ້າເງິນ
  pinMode(BUZZER_PIN, OUTPUT); // ກຳນົດໃຫ້ Buzzer ເປັນຂາ OUTPUT
  pinMode(RESET_BUTTON, INPUT_PULLUP); // ປຸ່ມ Reset (Input Pull-Up)
  for (int i = 0; i < 7; i++) {
    pinMode(segPins[i], OUTPUT); // ຂາ A-G ຂອງ 7-segment display
  }
}

void loop() {
  if (digitalRead(RESET_BUTTON) == LOW) { // ຖ້າກົດປຸ່ມ Reset
    resetSystem(); // ຟັງຊັນ Reset
    return; // ສິ້ນສຸດການເຮັດວຽກໃນ loop
  }

  int potValue = analogRead(POT_PIN); // ອ່ານຄ່າຈາກ Potentiometer
  int lightLevel = map(potValue, 0, 1023, 0, 100); // ແປງຄ່າເປັນຊ່ວງ 0-100
  showNumber(lightLevel); // ສະແດງລະດັບແສງທີ່ 7-segment display
  
  // ສະແດງຄ່າ lightLevel ໃນ Serial Monitor
  Serial.print("Light Level: ");
  Serial.println(lightLevel);
  
  // ຄວບຄຸມ RGB LED ແລະ Buzzer
  if (lightLevel < 33) {
    setRGB(1, 0, 0); // ສີແດງ
    noTone(BUZZER_PIN); // ຢຸດສຽງ
  } else if (lightLevel < 66) {
    setRGB(0, 1, 0); // ສີຂຽວ
    tone(BUZZER_PIN, 500); // ສົ່ງສຽງທີ່ຄວາມຖີຍ 500 Hz
  } else {
    setRGB(0, 0, 1); // ສີນໍ້າເງິນ
    tone(BUZZER_PIN, 2000); // ສົ່ງສຽງທີ່ຄວາມຖີຍ 2000 Hz
  }
  delay(100); // ຫນ່ວງເວລາ
}

void showNumber(int num) {
  int digits[3] = {0, 0, 0};     // ບັນທຶກເລກ 3 ຫຼັກ
  digits[0] = (num / 100) % 10;  // ຫຼັກຮ້ອຍ
  digits[1] = (num / 10) % 10;   // ຫຼັກສິບ
  digits[2] = num % 10;          // ຫຼັກໜ່ວຍ
  for (int i = 0; i < 3; i++) {
    displayDigit(digits[i]);     // ສະແດງເລກທີລະຫຼັກ
    delay(1000);                 // ຫນ່ວງເວລາ
  }
}

void displayDigit(int num) {
  for (int i = 0; i < 7; i++) {
    digitalWrite(segPins[i], segmentDigits[num][i]); // ສະແດງຄ່າຂາ A-G
  }
}

void setRGB(int r, int g, int b) {
  digitalWrite(RGB_R, r); // ຄວບຄຸມສີແດງ
  digitalWrite(RGB_G, g); // ຄວບຄຸມສີຂຽວ
  digitalWrite(RGB_B, b); // ຄວບຄຸມສີນໍ້າເງິນ
}

void resetSystem() {
  // Reset RGB LED
  setRGB(0, 0, 0);
  noTone(BUZZER_PIN); // ປິດສຽງທັງໝົດ
  // Reset 7-segment display
  for (int i = 0; i < 7; i++) {
    digitalWrite(segPins[i], LOW);
  }
}
```
![[code PJ 1.png]]
#### ຜົມລັບ
ຕອນໄຟສີແດງ
![[Pasted image 20250203042535.png]]
ຕອນໄຟສີຂຽວ
![[Pasted image 20250203042559.png]]
ຕອນໄຟສີຟ້າ
![[Pasted image 20250203042629.png]]
#### ນີ້ຄື Tinkercad ຂອງນ້ອງທີ່ທົດລອງ
[Tinkercad ຂອງນ້ອງເອງ Light Level Control System](https://www.tinkercad.com/things/5cs6uhtpucj-light-level-control-system?sharecode=qZDssiEnYX0VfQqciZe6UR5lfZl33S96I7QJwUA4k_g)
![[Pasted image 20250203042819.png]]