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
## 스위치를 많이 눌러라 게임
![](./images/I2C_2.png)
### Thinkercad
```c
#include <Adafruit_LiquidCrystal.h>

#define BUTTON_1P 10
#define BUTTON_2P 9
#define BUTTON_START 8

Adafruit_LiquidCrystal lcd(0);

unsigned int buttonCnt1 = 0;
unsigned int buttonCnt2 = 0;

unsigned long currTime = 0;
unsigned long prevTime = 0;

unsigned int gameTime = 30;

void setup() {
  pinMode(BUTTON_1P, INPUT_PULLUP);
  pinMode(BUTTON_2P, INPUT_PULLUP);
  pinMode(BUTTON_START, INPUT_PULLUP);
  lcd.begin(16, 2);
}

void loop() {
  if(button1P() == 1) {
    if(gameTime > 0 ) buttonCnt1++;
    delay(50);
  }
  if(button2P() == 1) {
    if(gameTime > 0 ) buttonCnt2++;
    delay(50);
  }
   if(buttonStart() == 1){
    gameTime = 30;
    buttonCnt1 = 0;
    buttonCnt2 = 0;
    delay(50);
  }

  currTime = millis();
  if(currTime - prevTime >= 1000) {
    prevTime = currTime;
    if(gameTime > 0) gameTime--;
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
    if(newSw == 0) {
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
    if(newSw == 0) {
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
## 포토 레지스터 : 빛 센서(CDS)
![](./images/cds.png)
```c
void setup()
{
  pinMode(9, OUTPUT); 
}

void loop()
{
  int val1 = analogRead(0);
  int val2 = map(val1, 0, 1023, 0, 255);
  
  analogWrite(9, val2);
  delay(20);
}
```

```c
int sensorValue = 0;

void setup()
{
  pinMode(A0, INPUT);
  Serial.begin(9600);
  pinMode(9, OUTPUT);
}

void loop()
{
  // read the value from the sensor
  sensorValue = analogRead(A0);
  // print the sensor reading so you know its range
  Serial.println(sensorValue);
  // map the sensor reading to a range for the LED
  analogWrite(9, map(sensorValue, 0, 1023, 0, 255));
  delay(100); // Wait for 100 millisecond(s)
}
```

```c
int sensorPin = A0;  //아날로그 핀은 입력 명시하지 않아도 됨.
int ledPin = 9;  // PWM
int lightLevel;


void setup()
{
    pinMode(ledPin, OUTPUT); 
}


void loop()
{
    lightLevel = analogRead(sensorPin); 
    adjustBrightness(); //LED 밝기 조절 함수
    analogWrite(ledPin, lightLevel); 
}


void adjustBrightness()
{
    lightLevel = map(lightLevel, 500, 1023, 0, 255);
    lightLevel = constrain(lightLevel, 0, 255);
      //lightlevel이 0보다 작으면 0을 출력 
      //255보다 크면 255를 출력, 0~255사이의 숫자면 그대로 출력
}
```

## 온도 센서 : TMP36 - 1
![](./images/tmp.png)
```c
int baselineTemp = 0;

int celsius = 0;

int fahrenheit = 0;

void setup()
{
  pinMode(A0, INPUT);
  Serial.begin(9600);
  pinMode(2, OUTPUT);
  pinMode(3, OUTPUT);
  pinMode(4, OUTPUT);
}

void loop()
{
  // set threshold temperature to activate LEDs
  baselineTemp = 40;
  // measure temperature in Celsius
  celsius = map(((analogRead(A0) - 20) * 3.04), 0, 1023, -40, 125);
  // convert to Fahrenheit
  fahrenheit = ((celsius * 9) / 5 + 32);
  Serial.print(celsius);
  Serial.print(" C, ");
  Serial.print(fahrenheit);
  Serial.println(" F");
  if (celsius < baselineTemp) {
    digitalWrite(2, LOW);
    digitalWrite(3, LOW);
    digitalWrite(4, LOW);
  }
  if (celsius >= baselineTemp && celsius < baselineTemp + 10) {
    digitalWrite(2, HIGH);
    digitalWrite(3, LOW);
    digitalWrite(4, LOW);
  }
  if (celsius >= baselineTemp + 10 && celsius < baselineTemp + 20) {
    digitalWrite(2, HIGH);
    digitalWrite(3, HIGH);
    digitalWrite(4, LOW);
  }
  if (celsius >= baselineTemp + 20 && celsius < baselineTemp + 30) {
    digitalWrite(2, HIGH);
    digitalWrite(3, HIGH);
    digitalWrite(4, HIGH);
  }
  if (celsius >= baselineTemp + 30) {
    digitalWrite(2, HIGH);
    digitalWrite(3, HIGH);
    digitalWrite(4, HIGH);
  }
  delay(1000); // Wait for 1000 millisecond(s)
}
```

## 초음파 센서(3pin)
![](./images/sig.png)
```c
/* 3핀 초음파센서 활용 */
int ultra = 7; // 출력 단자

void setup(){
  Serial.begin(9600);
}
void loop(){
  pinMode(ultra, OUTPUT);  
  //sig핀 기본출력설정
  digitalWrite(ultra, LOW);
  delay(1); // Wait for 100 millisecond(s), 
  
  digitalWrite(ultra, HIGH);
  delayMicroseconds(10);//센서를 동작하기 위한 시작신호로
  // sig핀에 5us동안 high신호 출력후 low출력
  digitalWrite(ultra, LOW);
  
  pinMode(ultra, INPUT);  
  
  //ultra 핀이 HIGH를 유지한 시간을 저장
  unsigned long duration = pulseIn(ultra, HIGH);
  
  //HIGH였을땨 시간(초음파가 보냈다가 다시 돌아온 시간)을 가지고 거리계산
  //초음파는 340m/s속도로 이동함에 따라(25도씨 기준)
  //이때 속도와 센서가 보낸 시간을 이용하여 거리를 계산
  //왕복해서 오는것이므로 결과를 2로 나누어 줌
  double dist = ((340*duration)/10000)/2;
    
  Serial.print("Distance:  ");
  Serial.print(dist);
  Serial.println("cm");
  delay(100);
}
```

## 초음파 센서(4pin)
![](./images/trig.png)
```c
/*거리에 따라 LED 표시*/

int trig = 2;          
int echo = 3;
int RED = 8;       
int YELLOW = 9;     
int GREEN = 10;    

void setup()
{
  Serial.begin(9600);
  pinMode(trig, OUTPUT);
  pinMode(echo, INPUT);
  pinMode(RED, OUTPUT);     
  pinMode(YELLOW, OUTPUT);
  pinMode(GREEN, OUTPUT);
}

void loop()
{
  digitalWrite(trig, HIGH);
  delayMicroseconds(10);
  digitalWrite(trig, LOW);

  // echoPin 이 HIGH를 유지한 시간을 저장 한다.
  unsigned long duration = pulseIn(echo, HIGH); 
  // HIGH 였을 때 시간(초음파가 보냈다가 다시 들어온 시간)을 가지고 거리를 계산 한다.
  float distance = ((float)(340 * duration) / 10000) / 2;  
  
  Serial.print(distance);
  Serial.println("cm");
  delay(100);
  
  if (distance > 80)  //   distance(거리) 가 80보다 크면
  {
    digitalWrite(GREEN, HIGH);     // GREEN이 연결된 핀에 HIGH 신호(5V)를,
    digitalWrite(YELLOW, LOW);    // YELLOW가 연결된 핀에 LOW 신호(0V)를,
    digitalWrite(RED, LOW);       // RED가 연결된 핀에 LOW신호(0V)를.
  }
  if (distance > 30 & distance <= 70)  
  {
    digitalWrite(GREEN, LOW);   
    digitalWrite(YELLOW, HIGH);
    digitalWrite(RED, LOW);
  }
  if (distance > 0 & distance <= 30)
  {
    digitalWrite(GREEN, LOW);
    digitalWrite(YELLOW, LOW);
    digitalWrite(RED, HIGH);
  }
}
```
## Ultrasonic sensor and 12C LCD
![](./images/lcd.png)
```c
#include <Adafruit_LiquidCrystal.h>
#define trigPin 2
#define echoPin 3
long duration;
int distance;
Adafruit_LiquidCrystal lcd_1(0);

void setup() {

  pinMode(trigPin, OUTPUT);
  pinMode(echoPin, INPUT);
  Serial.begin(9600);
  lcd_1.begin(16,2);
  lcd_1.print("Sensor Value :");
}

void loop() {

  digitalWrite(trigPin, LOW);
  delayMicroseconds(5);
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);
  duration = pulseIn(echoPin, HIGH);
  distance = duration * 0.034 / 2;
  lcd_1.setCursor(0,1);
  lcd_1.print(distance);
  lcd_1.print("cm - Andrea");
  delay(50);
}
```

## Ultrasonic sensor with LCD and Buzzer - 2
![](./images/lcdbuzzer.png)
```c
```c
// C++ code
//
#include <Adafruit_LiquidCrystal.h>

Adafruit_LiquidCrystal lcd(0); 

// Define the ultrasonic sensor pins
const int trigPin = 9;
const int echoPin = 10;

// Define the buzzer pin
const int buzzerPin = A0;

void setup() {
  Serial.begin(9600);
  lcd.begin(16,2);
  lcd.setCursor(0,0);
  lcd.print("Initializing...");

  pinMode(trigPin, OUTPUT);
  pinMode(echoPin, INPUT);
  pinMode(buzzerPin, OUTPUT);
  
  delay(2000);
  lcd.clear();
  lcd.print("Ready");
  delay(1000);
}

 void loop() {
  long duration, distance;
     digitalWrite(trigPin, LOW);
  delayMicroseconds(2);
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);
  duration = pulseIn(echoPin, HIGH); // measure the time it takes to travel 
  distance = duration * 0.034 / 2; //34cm ms divide 2 is half of total distance

  lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print("Distance: ");
  

  if (distance >= 70 || distance <= 0) {
    lcd.print("Out of range");
    noTone(buzzerPin); 
  } else {
    lcd.print(distance);
    lcd.print(" cm");


    if (distance > 60) {
      tone(buzzerPin, 2500); // Play a tone at 2500Hz for distances greater than 60 cm
    } else if (distance > 20) {
      tone(buzzerPin, 1500); // Play a tone at 1500Hz for distances between 20 and 60 cm
    } else if (distance > 1) {
      tone(buzzerPin, 500); // Play a tone at 500Hz for distances less than 20 cm
    }
  }

  delay(500);
}
```
