# 멜로디 예제 1
## tone() 함수를 이용하여 ‘도레미파솔라시도’출력
![](./images/Buzzer.png)

##  tone() 함수를 이용한 Source code
```c
void setup() {
  pinMode(7, OUTPUT);
}

void loop(){
  tone(7, 262, 1000);
  delay(500);
  tone(7, 294, 1000);
  delay(500);
  tone(7, 330, 1000);
  delay(500);
  tone(7, 349, 1000);
  delay(500);
  tone(7, 392, 1000);
  delay(500);
  tone(7, 440, 1000);
  delay(500);
  tone(7, 494, 1000);
  delay(500);
}
```
## 긴 악보의 연주곡
```c
const int piezo = 7;
char code[] = {'C', 'D', 'E', 'F', 'G', 'A', 'B'};
unsigned int freq[] = {262, 294, 330, 349, 392, 440, 494};
char mData[] = "CCGGAAGpFFEEDCCpGGFFEEDpGGFFEEDpCCGGAAGpFFEEDDCp";
const byte mSize = sizeof(mData);
void setup() {
   pinMode(piezo, OUTPUT);
}

void loop() {
  int playT = 200;
  for(int m=0; m<mSize; m++) {
    for(int k=0; k<7; k++) {
      if(mData[m] == code[k]) tone(piezo, freq[k], playT);
    }
    delay(playT);
  }
  noTone(piezo);
  delay(2000);
}
```

## tone()함수 없이 멜로디 출력(학교 종이 땡땡땡)
```c
const int piezo = 7;  // 피에조 스피커 핀

// 도레미파솔라시도 주파수
#define NOTE_C4  262
#define NOTE_D4  294
#define NOTE_E4  330
#define NOTE_F4  349
#define NOTE_G4  392
#define NOTE_A4  440
#define NOTE_B4  494
#define NOTE_C5  523

// 멜로디 배열 (학교 종이 땡땡땡)
int melody[] = {
  NOTE_C4, NOTE_C4, NOTE_G4, NOTE_G4, NOTE_A4, NOTE_A4, NOTE_G4, 0,
  NOTE_F4, NOTE_F4, NOTE_E4, NOTE_E4, NOTE_D4, NOTE_D4, NOTE_C4, 0,
  NOTE_G4, NOTE_G4, NOTE_F4, NOTE_F4, NOTE_E4, NOTE_E4, NOTE_D4, 0,
  NOTE_G4, NOTE_G4, NOTE_F4, NOTE_F4, NOTE_E4, NOTE_E4, NOTE_D4, 0,
  NOTE_C4, NOTE_C4, NOTE_G4, NOTE_G4, NOTE_A4, NOTE_A4, NOTE_G4, 0,
  NOTE_F4, NOTE_F4, NOTE_E4, NOTE_E4, NOTE_D4, NOTE_D4, NOTE_C4, 0
};

int noteDurations[] = {
  400, 400, 400, 400, 400, 400, 600, 200,
  400, 400, 400, 400, 400, 400, 600, 200,
  400, 400, 400, 400, 400, 400, 600, 200,
  400, 400, 400, 400, 400, 400, 600, 200,
  400, 400, 400, 400, 400, 400, 600, 200,
  400, 400, 400, 400, 400, 400, 600, 200
};

// 음을 직접 재생하는 함수 (tone 없이)
void playNote(int pin, unsigned int frequency, int duration) {
  if (frequency == 0) {
    delay(duration);  // 쉼표
    return;
  }

  unsigned long period = 1000000L / frequency;
  unsigned long cycles = (duration * 1000L) / period;

  for (unsigned long i = 0; i < cycles; i++) {
    digitalWrite(pin, HIGH);
    delayMicroseconds(period / 2);
    digitalWrite(pin, LOW);
    delayMicroseconds(period / 2);
  }
}

void setup() {
  pinMode(piezo, OUTPUT);
}

void loop() {
  int notes = sizeof(melody) / sizeof(melody[0]);

  for (int i = 0; i < notes; i++) {
    int freq = melody[i];
    int dur = noteDurations[i];

    playNote(piezo, freq, dur);
    delay(50); // 음 사이 쉼
  }

  delay(3000); // 한 곡 끝난 후 대기
}

```
## I2C LCD를 이용한 문자열 출력
![](./images/I2C.png)
```c
#include <Adafruit_LiquidCrystal.h>

int seconds = 0;

Adafruit_LiquidCrystal lcd_1(0);

void setup()
{
  lcd_1.begin(16, 2);

  lcd_1.print("hello world");
}

void loop()
{
  lcd_1.setCursor(0, 1);
  lcd_1.print(seconds);
  lcd_1.setBacklight(1);
  delay(500); // Wait for 500 millisecond(s)
  lcd_1.setBacklight(0);
  delay(500); // Wait for 500 millisecond(s)
  seconds += 1;
}
```
```C
#include <Wire.h>
#include <LiquidCrystal_I2C.h>


LiquidCrystal_I2C lcd(0x27, 16, 2);

void setup()
{
  lcd.init(); //LCD 초기화
  lcd.backlight();  //백라이트 켜기
  lcd.print("Hello, I2C");
}

void loop()
{
}
```
### 깜빡임 포함
```C
#include <Wire.h>
#include <LiquidCrystal_I2C.h>


LiquidCrystal_I2C lcd(0x27, 16, 2);

void setup()
{
  lcd.init(); //LCD 초기화
  lcd.backlight();  //백라이트 켜기
  lcd.print("Hello, I2C");
}

void loop()
{
  lcd.setCursor(0, 1);
  //lcd.print("Blinking LCD");

  lcd.backlight();       // 백라이트 켜기
  delay(500);

  lcd.noBacklight();     // 백라이트 끄기
  delay(500);
}
```
