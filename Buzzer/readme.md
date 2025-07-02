# 멜로디 예제 1
## tone() 함수를 이용하여 ‘도레미파솔라시도’출력
![](./images/Buzzer.png)

##  tone() 함수를 이용한 Source code
```c
void setup() {
  pinMode(7, OUTPUT);  // 디지털 핀 7번을 출력 모드로 설정 (스피커 연결용)
}

void loop(){
  tone(7, 262, 1000); // 7번 핀에서 262Hz 음을 1000ms(1초) 동안 재생 (도 음)
  delay(500);         // 0.5초 대기 (음이 끝나기 전에 약간 쉬는 시간)
  
  tone(7, 294, 1000); // 294Hz 음 재생 (레 음)
  delay(500);
  
  tone(7, 330, 1000); // 330Hz 음 재생 (미 음)
  delay(500);
  
  tone(7, 349, 1000); // 349Hz 음 재생 (파 음)
  delay(500);
  
  tone(7, 392, 1000); // 392Hz 음 재생 (솔 음)
  delay(500);
  
  tone(7, 440, 1000); // 440Hz 음 재생 (라 음)
  delay(500);
  
  tone(7, 494, 1000); // 494Hz 음 재생 (시 음)
  delay(500);
}
```
## 긴 악보의 연주곡
```c
const int piezo = 7;  // 부저(피에조) 연결 핀 번호

// 음계 코드 배열 (도, 레, 미, 파, 솔, 라, 시)
char code[] = {'C', 'D', 'E', 'F', 'G', 'A', 'B'};

// 각 음계에 해당하는 주파수 배열 (Hz 단위)
unsigned int freq[] = {262, 294, 330, 349, 392, 440, 494};

// 연주할 멜로디 데이터 (문자열)
// 'p'는 쉼표(rest)를 의미함
char mData[] = "CCGGAAGpFFEEDCCpGGFFEEDpGGFFEEDpCCGGAAGpFFEEDDCp";

// 멜로디 데이터 크기 (바이트 수)
const byte mSize = sizeof(mData);

void setup() {
   pinMode(piezo, OUTPUT);  // 부저 핀을 출력으로 설정
}

void loop() {
  int playT = 200;  // 한 음을 재생하는 시간 (200ms)

  // 멜로디 데이터 배열을 처음부터 끝까지 반복
  for(int m=0; m<mSize; m++) {
    // 음계 코드 배열을 돌면서 현재 멜로디 문자가 어떤 음계인지 확인
    for(int k=0; k<7; k++) {
      if(mData[m] == code[k]) 
        tone(piezo, freq[k], playT);  // 해당 음계 주파수로 음 재생
    }
    delay(playT);  // 음 재생 시간만큼 대기
  }

  noTone(piezo);    // 모든 음 재생 후 부저 음 끄기
  delay(2000);      // 멜로디가 끝난 후 2초 휴식
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
#include <Adafruit_LiquidCrystal.h>  // Adafruit LCD 라이브러리 포함

int seconds = 0;  // 초를 저장할 변수

Adafruit_LiquidCrystal lcd_1(0); // LCD 객체 생성 (I2C 주소 0번)

void setup()
{
  lcd_1.begin(16, 2);  // LCD를 16열 2행으로 초기화

  lcd_1.print("hello world");  // LCD 첫 줄에 "hello world" 출력
}

void loop()
{
  lcd_1.setCursor(0, 1);    // 커서를 두 번째 줄 첫 번째 칸으로 이동
  lcd_1.print(seconds);     // 현재 초(seconds) 값을 출력

  lcd_1.setBacklight(1);    // LCD 백라이트 켜기
  delay(500);               // 0.5초 대기

  lcd_1.setBacklight(0);    // LCD 백라이트 끄기
  delay(500);               // 0.5초 대기

  seconds += 1;             // 초 변수 1 증가
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
## 스위치를 많이 눌러라 게임
![](./images/I2C_2.png)
### Thinkercad
```c
#include <Adafruit_LiquidCrystal.h>  // Adafruit LCD 라이브러리 포함

// 버튼 핀 번호 정의
#define BUTTON_1P 10        // 1P 버튼 핀
#define BUTTON_2P 9         // 2P 버튼 핀
#define BUTTON_START 8      // 시작 버튼 핀

Adafruit_LiquidCrystal lcd(0);  // LCD 객체 생성 (I2C 주소 0번)

unsigned int buttonCnt1 = 0;    // 1P 버튼 누른 횟수 카운터
unsigned int buttonCnt2 = 0;    // 2P 버튼 누른 횟수 카운터

unsigned long currTime = 0;     // 현재 시간 (밀리초 단위)
unsigned long prevTime = 0;     // 이전 시간 저장용 변수

unsigned int gameTime = 30;     // 게임 시간 (초 단위), 초기값 30초

void setup() {
  // 버튼 핀을 내부 풀업 저항을 사용해 입력으로 설정
  pinMode(BUTTON_1P, INPUT_PULLUP);
  pinMode(BUTTON_2P, INPUT_PULLUP);
  pinMode(BUTTON_START, INPUT_PULLUP);

  lcd.begin(16, 2);  // LCD 16x2 문자 모드로 초기화
}

void loop() {
  // 1P 버튼 눌림 감지
  if(button1P() == 1) {
    if(gameTime > 0) buttonCnt1++;  // 게임 시간이 남아있으면 1P 카운터 증가
    delay(50);                      // 버튼 디바운싱용 짧은 딜레이
  }

  // 2P 버튼 눌림 감지
  if(button2P() == 1) {
    if(gameTime > 0) buttonCnt2++;  // 게임 시간이 남아있으면 2P 카운터 증가
    delay(50);                      // 버튼 디바운싱용 짧은 딜레이
  }

  // 시작 버튼 눌림 감지
  if(buttonStart() == 1) {
    gameTime = 30;          // 게임 시간 30초로 초기화
    buttonCnt1 = 0;         // 1P 카운터 초기화
    buttonCnt2 = 0;         // 2P 카운터 초기화
    delay(50);              // 버튼 디바운싱용 딜레이
  }

  currTime = millis();       // 현재 시간 읽기 (밀리초)
  
  // 1초가 지났으면 (1000ms)
  if(currTime - prevTime >= 1000) {
    prevTime = currTime;     // 이전 시간 갱신
    
    if(gameTime > 0) gameTime--;  // 게임 시간이 남아있으면 1초 감소
    
    lcd.clear();             // LCD 화면 초기화
    
    lcd.setCursor(3, 0);     // 첫 줄 4번째 칸으로 커서 이동
    lcd.print(" TIME: ");    // " TIME: " 출력
    lcd.print(gameTime);     // 남은 시간 출력
    
    lcd.setCursor(0, 1);     // 두 번째 줄 첫 칸으로 커서 이동
    lcd.print(" 1P: ");      // 1P 레이블 출력
    lcd.print(buttonCnt1);   // 1P 버튼 누른 횟수 출력
    
    lcd.setCursor(8, 1);     // 두 번째 줄 9번째 칸으로 커서 이동
    lcd.print(" 2P: ");      // 2P 레이블 출력
    lcd.print(buttonCnt2);   // 2P 버튼 누른 횟수 출력
  }
}

// 1P 버튼 눌림 감지 함수 (변화 감지 후 눌림 상태 확인)
int button1P() {
  static int oldSw = 1;  // 이전 버튼 상태 저장 (초기값 HIGH)
  static int newSw = 1;  // 현재 버튼 상태 저장
  newSw = digitalRead(BUTTON_1P);  // 버튼 핀 상태 읽기
  
  // 상태가 변했을 때
  if (newSw != oldSw) {
    oldSw = newSw;      // 이전 상태 갱신
    if(newSw == 0) {    // 버튼이 눌린 상태이면 (LOW)
      return 1;         // 눌림 신호 반환
    }
  }
  return 0;             // 눌리지 않음
}

// 2P 버튼 눌림 감지 함수 (변화 감지 후 눌림 상태 확인)
int button2P() {
  static int oldSw = 1;
  static int newSw = 1;
  newSw = digitalRead(BUTTON_2P);
  
  if (newSw != oldSw) {
    oldSw = newSw;
    if(newSw == 0) {
      return 1;
    }
  }
  return 0;
}

// 시작 버튼 눌림 감지 함수 (변화 감지 후 눌림 상태 확인)
int buttonStart() {
  static int oldSw = 1;
  static int newSw = 1;
  newSw = digitalRead(BUTTON_START);
  
  if (newSw != oldSw) {
    oldSw = newSw;
    if(newSw == 0) {
      return 1;
    }
  }
  return 0;
}
```
### 아두이노
```c
#include <LiquidCrystal_I2C.h>

#define BUTTON_1P 10
#define BUTTON_2P 9
#define BUTTON_START 8

LiquidCrystal_I2C lcd(0x27, 16, 2);

unsigned int buttonCnt1 = 0;
unsigned int buttonCnt2 = 0;

unsigned long currTime = 0;
unsigned long prevTime = 0;

unsigned int gameTime = 30;

void setup() {
  lcd.init();
  pinMode(BUTTON_1P, INPUT_PULLUP);
  pinMode(BUTTON_2P, INPUT_PULLUP);
  pinMode(BUTTON_START, INPUT_PULLUP);
  lcd.begin(16, 2);
}

void loop() {
  if (button1P() == 1) {
    if (gameTime > 0) buttonCnt1++;
    delay(50);
  }
  if (button2P() == 1) {
    if (gameTime > 0) buttonCnt2++;
    delay(50);
  }
  if (buttonStart() == 1) {
    gameTime = 30;
    buttonCnt1 = 0;
    buttonCnt2 = 0;
    delay(50);
  }

  currTime = millis();
  if (currTime - prevTime >= 1000) {
    prevTime = currTime;
    if (gameTime > 0) gameTime--;
    lcd.clear();
    lcd.setCursor(3, 0);
    lcd.print(" TIME: ");
    lcd.print(gameTime);
    lcd.setCursor(0, 1);
    lcd.print(" 1P: ");
    lcd.print(buttonCnt1);
    lcd.setCursor(8, 1);
    lcd.print(" 2P: ");
    lcd.print(buttonCnt2);
  }
}
int button1P() {
  static int oldSw = 1;
  static int newSw = 1;
  newSw = digitalRead(BUTTON_1P);
  if (newSw != oldSw) {
    oldSw = newSw;
    if (newSw == 0) {
      return 1;
    }
  }
  return 0;
}
int button2P() {
  static int oldSw = 1;
  static int newSw = 1;
  newSw = digitalRead(BUTTON_2P);
  if (newSw != oldSw) {
    oldSw = newSw;
    if (newSw == 0) {
      return 1;
    }
  }
  return 0;
}
int buttonStart() {
  static int oldSw = 1;
  static int newSw = 1;
  newSw = digitalRead(BUTTON_START);
  if (newSw != oldSw) {
    oldSw = newSw;
    if (newSw == 0) {
      return 1;
    }
  }
  return 0;
}
```
