# sensor예제
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
int baselineTemp = 0;    // 기준 온도 설정 변수

int celsius = 0;         // 섭씨 온도 저장 변수

int fahrenheit = 0;      // 화씨 온도 저장 변수

void setup()
{
  pinMode(A0, INPUT);    // 아날로그 입력 A0핀을 입력 모드로 설정 (온도 센서 연결)
  Serial.begin(9600);    // 시리얼 통신 9600bps로 시작
  pinMode(2, OUTPUT);    // LED 제어용 핀 2 출력 모드 설정
  pinMode(3, OUTPUT);    // LED 제어용 핀 3 출력 모드 설정
  pinMode(4, OUTPUT);    // LED 제어용 핀 4 출력 모드 설정
}

void loop()
{
  // LED 작동 기준 온도 설정
  baselineTemp = 40;

  // 온도를 섭씨로 측정
  // analogRead(A0)에서 읽은 값을 보정 후 3.04 곱해서 0~1023 범위를 -40~125도 범위로 맵핑
  celsius = map(((analogRead(A0) - 20) * 3.04), 0, 1023, -40, 125);

  // 섭씨를 화씨로 변환 공식 적용
  fahrenheit = ((celsius * 9) / 5 + 32);

  // 측정된 온도 값을 시리얼 모니터에 출력
  Serial.print(celsius);
  Serial.print(" C, ");
  Serial.print(fahrenheit);
  Serial.println(" F");

  // 온도에 따른 LED 점등 제어
  if (celsius < baselineTemp) {
    // 기준 온도보다 낮으면 모든 LED 끄기
    digitalWrite(2, LOW);
    digitalWrite(3, LOW);
    digitalWrite(4, LOW);
  }
  if (celsius >= baselineTemp && celsius < baselineTemp + 10) {
    // 기준 온도 이상 10도 미만이면 LED 2만 켬
    digitalWrite(2, HIGH);
    digitalWrite(3, LOW);
    digitalWrite(4, LOW);
  }
  if (celsius >= baselineTemp + 10 && celsius < baselineTemp + 20) {
    // 기준 온도 +10 이상 20도 미만이면 LED 2, 3 켬
    digitalWrite(2, HIGH);
    digitalWrite(3, HIGH);
    digitalWrite(4, LOW);
  }
  if (celsius >= baselineTemp + 20 && celsius < baselineTemp + 30) {
    // 기준 온도 +20 이상 30도 미만이면 LED 2, 3, 4 모두 켬
    digitalWrite(2, HIGH);
    digitalWrite(3, HIGH);
    digitalWrite(4, HIGH);
  }
  if (celsius >= baselineTemp + 30) {
    // 기준 온도 +30 이상이면 LED 2, 3, 4 모두 켬 (최고 온도 상태)
    digitalWrite(2, HIGH);
    digitalWrite(3, HIGH);
    digitalWrite(4, HIGH);
  }

  delay(1000); // 1초간 대기 (측정 및 표시 간격)
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

#define trigPin 2    // 초음파 센서의 트리거 핀 번호 정의
#define echoPin 3    // 초음파 센서의 에코 핀 번호 정의

long duration;       // 초음파 신호가 왕복하는 데 걸린 시간(마이크로초)
int distance;        // 계산된 거리(cm)

Adafruit_LiquidCrystal lcd_1(0);  // LCD 객체 생성 (I2C 주소 0)

void setup() {
  pinMode(trigPin, OUTPUT);   // 트리거 핀을 출력 모드로 설정
  pinMode(echoPin, INPUT);    // 에코 핀을 입력 모드로 설정
  Serial.begin(9600);         // 시리얼 통신 시작 (디버깅용)
  lcd_1.begin(16,2);          // LCD 초기화 (16x2 크기)
  lcd_1.print("Sensor Value :");  // LCD 첫 줄에 텍스트 출력
}

void loop() {
  // 초음파 센서에 신호 보내기
  digitalWrite(trigPin, LOW);
  delayMicroseconds(5);       // 잠시 대기
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);      // 10마이크로초 동안 HIGH 신호 유지
  digitalWrite(trigPin, LOW);

  // 에코 핀에서 신호가 HIGH인 시간 측정
  duration = pulseIn(echoPin, HIGH);

  // 시간(duration)을 거리(cm)로 변환 (음속 340m/s 기준)
  distance = duration * 0.034 / 2;

  // LCD 두 번째 줄에 거리 출력
  lcd_1.setCursor(0,1);
  lcd_1.print(distance);
  lcd_1.print("cm - Andrea");

  delay(50);  // 50밀리초 대기 후 다시 측정
}
```

## Ultrasonic sensor with LCD and Buzzer - 2
![](./images/lcdbuzzer.png)
```c
// C++ 코드
//
#include <Adafruit_LiquidCrystal.h>

Adafruit_LiquidCrystal lcd(0);  // LCD 객체 생성 (I2C 주소 0)

// 초음파 센서 핀 정의
const int trigPin = 9;
const int echoPin = 10;

// 버저 핀 정의
const int buzzerPin = A0;

void setup() {
  Serial.begin(9600);
  lcd.begin(16,2);               // LCD 초기화 (16x2 크기)
  lcd.setCursor(0,0);
  lcd.print("Initializing...");  // 초기화 메시지 출력

  pinMode(trigPin, OUTPUT);      // 트리거 핀 출력 모드 설정
  pinMode(echoPin, INPUT);       // 에코 핀 입력 모드 설정
  pinMode(buzzerPin, OUTPUT);    // 버저 핀 출력 모드 설정
  
  delay(2000);                   // 2초 대기
  lcd.clear();
  lcd.print("Ready");            // 준비 완료 메시지 출력
  delay(1000);
}

void loop() {
  long duration, distance;
  
  // 초음파 신호 보내기
  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);
  
  // 에코 핀에서 신호가 HIGH인 시간 측정
  duration = pulseIn(echoPin, HIGH); 
  
  // 시간(duration)을 거리(cm)로 변환 (음속 340m/s 기준)
  distance = duration * 0.034 / 2; 

  lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print("Distance: ");       // 거리 표시 텍스트 출력

  if (distance >= 70 || distance <= 0) {   // 거리 측정 범위 밖일 때
    lcd.print("Out of range");              // 범위 밖 메시지 출력
    noTone(buzzerPin);                      // 버저 소리 끄기
  } else {
    lcd.print(distance);                    // 측정된 거리 출력
    lcd.print(" cm");

    // 거리별 버저 소리 제어
    if (distance > 60) {
      tone(buzzerPin, 2500); // 60cm 초과 시 2500Hz 소리 재생
    } else if (distance > 20) {
      tone(buzzerPin, 1500); // 20~60cm 사이 1500Hz 소리 재생
    } else if (distance > 1) {
      tone(buzzerPin, 500);  // 1~20cm 사이 500Hz 소리 재생
    }
  }

  delay(500);  // 0.5초 대기
}
```
## DC Motor
![](./images/DCmotor.png)
```c
void setup() 
{
  pinMode(9, OUTPUT); // 9번 핀을 출력 모드로 설정 (PWM 신호 출력용)
}

void loop()
{
  Serial.begin(9600); // 시리얼 통신 시작 (속도: 9600bps)

  int inputValue = analogRead(A0); // 아날로그 입력 A0 핀에서 값을 읽음 (0~1023 범위)
  Serial.println(inputValue);      // 읽은 값을 시리얼 모니터에 출력

  // 입력값(0~1023)을 PWM 출력 범위(0~255)로 변환
  int convertedValue = map(inputValue, 0, 1023, 0, 255);

  // 변환된 값을 9번 핀에 PWM 신호로 출력 (예: LED 밝기 조절, 모터 속도 조절)
  analogWrite(9, convertedValue);

  delay(100); // 100ms 대기 (반응 속도 조절용)
}
```
![](./images/DCmotor2.png)
```c
void setup() 
{
  pinMode(9, OUTPUT);
  pinMode(10, OUTPUT);
  pinMode(8, INPUT_PULLUP); // 이 부분이 핵심
}
void loop()
{
  int inputValue = analogRead(A0);
  Serial.println(inputValue);
  int convertedValue = map(inputValue, 0, 1023, 0, 255);

  int inputSwitch = digitalRead(8);
  if(inputSwitch == LOW) {	
    analogWrite(9, convertedValue);  //정회전
    analogWrite(10, 0);
  }
  else {
    analogWrite(9, 0); //역회전
    analogWrite(10, convertedValue);
  }   
  delay(100);
}
```
![](./images/DCmotor3.png)
```c
// 모터 제어 핀 정의
const int MOTOR_PIN_A = 5;  // 모터 제어용 PWM 핀 A
const int MOTOR_PIN_B = 6;  // 모터 제어용 PWM 핀 B

void setup() 
{
  // 모터 제어 핀을 출력으로 설정
  pinMode(MOTOR_PIN_A, OUTPUT);
  pinMode(MOTOR_PIN_B, OUTPUT);
}

void loop()
{
  // 디지털 입력 핀 4에서 값을 읽음 (예: 버튼이나 센서)
  int readValue = digitalRead(4);

  // 입력값이 LOW(0)일 때
  if(readValue == LOW) {	
    analogWrite(MOTOR_PIN_A, 255); // A핀에 최대 출력 (모터 한 방향 회전)
    analogWrite(MOTOR_PIN_B, 0);   // B핀은 정지
  }
  else {
    analogWrite(MOTOR_PIN_A, 0);   // A핀은 정지
    analogWrite(MOTOR_PIN_B, 255); // B핀에 최대 출력 (모터 반대 방향 회전)
  }   

  delay(100);  // 100ms 대기 (너무 빠른 전환 방지용)
}
```
![](./images/DCmotor4.png)
```c
// 모터 A 연결 핀 설정
int enA = 9;   // 모터 A 속도 제어 핀 (PWM)
int in1 = 8;   // 모터 A 방향 제어 핀 1
int in2 = 7;   // 모터 A 방향 제어 핀 2

// 모터 B 연결 핀 설정
int enB = 3;   // 모터 B 속도 제어 핀 (PWM)
int in3 = 5;   // 모터 B 방향 제어 핀 1
int in4 = 4;   // 모터 B 방향 제어 핀 2

void setup() {
  // 모든 모터 제어 핀을 출력 모드로 설정
  pinMode(enA, OUTPUT);
  pinMode(enB, OUTPUT);
  pinMode(in1, OUTPUT);
  pinMode(in2, OUTPUT);
  pinMode(in3, OUTPUT);
  pinMode(in4, OUTPUT);

  // 모터 정지 상태로 초기화
  digitalWrite(in1, LOW);
  digitalWrite(in2, LOW);
  digitalWrite(in3, LOW);
  digitalWrite(in4, LOW);
}

void loop() {
  directionControl();  // 모터 회전 방향 제어
  delay(1000);         // 1초 대기
  speedControl();      // 모터 속도 제어
  delay(1000);         // 1초 대기
}

// 모터 회전 방향을 제어하는 함수
void directionControl() {
  // 모터 속도를 최대(PWM 값 255)로 설정
  analogWrite(enA, 255);
  analogWrite(enB, 255);

  // 모터 A, B 전진 방향으로 회전
  digitalWrite(in1, HIGH);
  digitalWrite(in2, LOW);
  digitalWrite(in3, HIGH);
  digitalWrite(in4, LOW);
  delay(2000); // 2초 회전

  // 모터 방향 반전 (후진)
  digitalWrite(in1, LOW);
  digitalWrite(in2, HIGH);
  digitalWrite(in3, LOW);
  digitalWrite(in4, HIGH);
  delay(2000); // 2초 회전

  // 모터 정지
  digitalWrite(in1, LOW);
  digitalWrite(in2, LOW);
  digitalWrite(in3, LOW);
  digitalWrite(in4, LOW);
}

// 모터 속도를 제어하는 함수
void speedControl() {
  // 모터 방향 설정 (후진)
  digitalWrite(in1, LOW);
  digitalWrite(in2, HIGH);
  digitalWrite(in3, LOW);
  digitalWrite(in4, HIGH);

  // 점점 빠르게 (가속)
  for (int i = 0; i < 256; i++) {
    analogWrite(enA, i); // 모터 A 속도 증가
    analogWrite(enB, i); // 모터 B 속도 증가
    delay(20);           // 20ms 대기
  }

  // 점점 느리게 (감속)
  for (int i = 255; i >= 0; --i) {
    analogWrite(enA, i); // 모터 A 속도 감소
    analogWrite(enB, i); // 모터 B 속도 감소
    delay(20);           // 20ms 대기
  }

  // 모터 정지
  digitalWrite(in1, LOW);
  digitalWrite(in2, LOW);
  digitalWrite(in3, LOW);
  digitalWrite(in4, LOW);
}

```
## DC Motor with Encoder
![](./images/encoder.png)
```c
#include <Encoder.h>           // 엔코더 라이브러리 포함

Encoder encoder(2, 3);         // 핀 2, 3번에 엔코더 연결 (UNO에서는 반드시 2, 3 사용)

long position = 0;             // 현재 모터 위치 (10스텝 당 1회전이라고 가정)
int rotation = 0;              // 회전 수 저장 변수

void setup()
{
  Serial.begin(9600);          // 시리얼 통신 시작 (9600bps)
  
  pinMode(5, OUTPUT);          // 모터 제어 핀 5번을 출력으로 설정
  pinMode(6, OUTPUT);          // 모터 제어 핀 6번을 출력으로 설정
}

void loop()
{
  // 모터를 한 방향으로 회전 (5번 핀에 PWM 신호, 6번 핀 LOW)
  analogWrite(5, 30);          // 모터에 30/255 속도로 회전 명령
  analogWrite(6, 0);

  // 엔코더에서 현재 위치값을 읽고 10으로 나눠 위치 단순화
  position = encoder.read() / 10;

  // 회전 수 계산 (절대값 사용, 10 스텝 = 1회전으로 가정)
  rotation = abs(position) / 10;

  // 현재 위치 출력
  Serial.print("pos: ");
  Serial.println(position);

  // 현재까지의 회전 수 출력
  Serial.print("rot: ");
  Serial.println(rotation);
}

```
## Vibrator Motor
![](./images/vmotor.png)

## Micro Servo
![](./images/servo.png)
```c
/*
  Sweep
  by BARRAGAN <http://barraganstudio.com>
  This example code is in the public domain.
  modified 8 Nov 2013  by Scott Fitzgerald
  http://www.arduino.cc/en/Tutorial/Sweep
*/
#include <Servo.h> // 서보 모터 제어용 라이브러리 포함

int pos = 0;         // 서보 위치값 저장 변수
Servo servo_9;       // 서보 객체 생성 (핀 9번 사용)

void setup() {
  servo_9.attach(9, 500, 2500); // 서보 핀 9번에 연결, 펄스 폭 범위 설정
}

void loop() {
  // 서보를 0도부터 180도까지 1도씩 증가시키며 이동
  for (pos = 0; pos <= 180; pos += 1) {
    servo_9.write(pos);   // 현재 pos 각도로 서보 회전
    delay(15);            // 15밀리초 대기 (서보가 움직일 시간 확보)
  }

  // 서보를 180도부터 0도까지 1도씩 감소시키며 이동
  for (pos = 180; pos >= 0; pos -= 1) {
    servo_9.write(pos);   // 현재 pos 각도로 서보 회전
    delay(15);            // 15밀리초 대기
  }
}
```
![](./images/servo2.png)
```c
#include <Servo.h> // Servo 라이브러리 사용

Servo myservo; // 서보 모터 객체 생성 (myservo라는 이름으로)

void setup(){
  myservo.attach(6); // 서보 모터를 Arduino의 D6번 핀에 연결
}

void loop(){
  // 서보를 시계 방향으로 0도에서 180도까지 회전
  for(int ang = 0; ang < 180; ang++) {
    myservo.write(ang);   // 현재 각도로 서보 회전
    delay(50);            // 0.05초 대기 (회전 동작 시간 확보)
  }

  // 서보를 반시계 방향으로 180도에서 0도까지 회전
  for(int ang = 180; ang > 0; ang--) {
    myservo.write(ang);   // 현재 각도로 서보 회전
    delay(50);            // 0.05초 대기
  }
}
```
![](./images/servo3.png)
```c
#include <Servo.h> // 서보 모터 제어를 위한 라이브러리

// 서보 객체 생성 (각각의 핀에 서보 모터 연결)
Servo servo_5;
Servo servo_4;
Servo servo_3;
Servo servo_2;

void setup()
{
  pinMode(A3, INPUT);     // A3 핀을 입력으로 설정 (가변저항)
  servo_5.attach(11);     // 서보_5를 D11번 핀에 연결

  pinMode(A2, INPUT);     
  servo_4.attach(10);     // 서보_4를 D10번 핀에 연결

  pinMode(A1, INPUT);     
  servo_3.attach(9);      // 서보_3를 D9번 핀에 연결

  pinMode(A0, INPUT);     
  servo_2.attach(8);      // 서보_2를 D8번 핀에 연결
}

void loop()
{
  // 아날로그 입력값(0~1023)을 0~180도로 변환하여 서보에 전달
  servo_5.write(map(analogRead(A3), 0, 1023, 0, 180)); 
  servo_4.write(map(analogRead(A2), 0, 1023, 0, 180));
  servo_3.write(map(analogRead(A1), 0, 1023, 0, 180));
  servo_2.write(map(analogRead(A0), 0, 1023, 0, 180));

  delay(10); // 너무 빠른 연산으로 시뮬레이션이 느려지는 것을 방지
}
```
![](./images/servo4.png)
```c
#include <Servo.h>             // 서보 모터 제어를 위한 라이브러리 포함

Servo servoMotor;              // 서보 객체 생성 (이름: servoMotor)
int potPos;                    // 가변저항 값을 저장할 변수

void setup() {
  servoMotor.attach(9);        // 서보 모터 신호선을 Arduino 9번 핀에 연결
}

void loop() {
  potPos = map(analogRead(A0), 0, 1023, 0, 180);  // A0 핀에서 가변저항 값 읽어 0~180도로 변환
  servoMotor.write(potPos);                        // 변환된 각도로 서보 모터 회전 명령
  delay(15);                                       // 15ms 대기 (서보가 움직일 시간 확보)
}

```
## Hobby Gearmotor
![](./images/hobby.png)

## 7-segment display(ca): Count Down Alarm
![](./images/7seg.png)
```c
int index = 0;       // LED 핀 인덱스용 변수
int counter;         // 루프 카운터 변수

// 숫자 0~9를 나타내는 LED ON/OFF 패턴 (8비트 문자열)
String pattern[] = {
  "00100001",   // 0
  "11111001",   // 1
  "00010011",   // 2
  "01010001",   // 3
  "11001001",   // 4
  "01000101",   // 5
  "00000105",   // 6
  "11110001",   // 7
  "00000001",   // 8
  "01000001"    // 9
};

// 모든 LED를 꺼주는 함수
void clear() {
  index = 0;
  for (counter = 0; counter < 8; ++counter) {
    analogWrite(index + 2, 255);  // 밝기를 최대 → OFF로 간주
    index++;
  }
}

// 숫자를 받아서 해당 숫자 패턴으로 LED를 표시
void display(int num) {
  clear();  // 먼저 LED 초기화
  index = 0;
  for (counter = 0; counter < 8; ++counter) {
    if (pattern[num].substring(index, index + 1) == "0") {
      analogWrite(index + 2, 0);   // 해당 자리가 0이면 LED 켜기
    }
    index++;
  }
}

void setup() {
  // 2~9번 핀을 출력으로 설정 (총 8개의 LED 제어)
  pinMode(2, OUTPUT);
  pinMode(3, OUTPUT);
  pinMode(4, OUTPUT);
  pinMode(5, OUTPUT);
  pinMode(6, OUTPUT);
  pinMode(7, OUTPUT);
  pinMode(8, OUTPUT);
  pinMode(9, OUTPUT);

  // 숫자 9부터 0까지 1초씩 표시
  display(9); delay(1000);
  display(8); delay(1000);
  display(7); delay(1000);
  display(6); delay(1000);
  display(5); delay(1000);
  display(4); delay(1000);
  display(3); delay(1000);
  display(2); delay(1000);
  display(1); delay(1000);
  display(0); delay(1000);

  // 마지막에 소리 재생 (도 음, 523Hz, 0.5초)
  tone(10, 523, 500);
}

void loop() {
  // 반복 동작 없음
}

```

## MISA 7-Segment 4-Digit LED Display
![](./images/7seg2.png)
```c
/* 6-13-2011  Spark Fun Electronics 2011  -- Nathan Seidle */

// 4자리 7-세그먼트 공통 애노드(또는 캐소드) 디스플레이 제어용 핀 설정
int digit1 = 13; // 첫 번째 자리 제어 핀
int digit2 = 12; // 두 번째 자리 제어 핀
int digit3 = 11; // 세 번째 자리 제어 핀
int digit4 = 10; // 네 번째 자리 제어 핀

// 7-세그먼트 각 세그먼트 핀 매핑
int segA = 7;  // 세그먼트 A
int segB = 6;  // 세그먼트 B
int segC = 3;  // 세그먼트 C
int segD = 4;  // 세그먼트 D
int segE = 5;  // 세그먼트 E
int segF = 8;  // 세그먼트 F
int segG = 9;  // 세그먼트 G
int segDP = 2; // 소수점 세그먼트 (사용 안 함)

void setup() {                
  // 세그먼트 핀들을 출력으로 설정
  pinMode(segA, OUTPUT);
  pinMode(segB, OUTPUT);
  pinMode(segC, OUTPUT);
  pinMode(segD, OUTPUT);
  pinMode(segE, OUTPUT);
  pinMode(segF, OUTPUT);
  pinMode(segG, OUTPUT);

  // 자리 선택 핀들을 출력으로 설정
  pinMode(digit1, OUTPUT);
  pinMode(digit2, OUTPUT);
  pinMode(digit3, OUTPUT);
  pinMode(digit4, OUTPUT);

  pinMode(13, OUTPUT);  // 내장 LED (사용하지 않음)
}

void loop() 
{
  // 아날로그 입력 A5핀(=5)에서 값을 읽어 7-세그먼트에 표시
  displayNumber( analogRead(5) );
}

void displayNumber(int toDisplay) {
  #define DISPLAY_BRIGHTNESS  500  // 각 자리 표시 시간(마이크로초)

  #define DIGIT_ON  HIGH   // 자리 선택 핀 켜기
  #define DIGIT_OFF  LOW   // 자리 선택 핀 끄기

  long beginTime = millis();

  // 4자리 각각을 빠르게 반복해서 켜서 숫자 표시 (다중화)
  for(int digit = 4 ; digit > 0 ; digit--) {

    // 현재 자리 켜기
    switch(digit) {
      case 1:
        digitalWrite(digit1, DIGIT_ON);
        break;
      case 2:
        digitalWrite(digit2, DIGIT_ON);
        break;
      case 3:
        digitalWrite(digit3, DIGIT_ON);
        break;
      case 4:
        digitalWrite(digit4, DIGIT_ON);
        break;
    }

    // toDisplay의 마지막 자리 숫자를 표시 (0~9)
    lightNumber(toDisplay % 10);

    // 표시할 숫자에서 마지막 자리 제거
    toDisplay /= 10;

    // 현재 자리 표시 유지 (지연시간, 밝기 조절용)
    delayMicroseconds(DISPLAY_BRIGHTNESS);

    // 모든 세그먼트 끄기
    lightNumber(10);

    // 모든 자리 끄기
    digitalWrite(digit1, DIGIT_OFF);
    digitalWrite(digit2, DIGIT_OFF);
    digitalWrite(digit3, DIGIT_OFF);
    digitalWrite(digit4, DIGIT_OFF);
  }

  // 약 10ms 동안 대기해서 화면 깜빡임 방지
  while( (millis() - beginTime) < 10) ;
}

// 숫자에 따라 7-세그먼트 각 세그먼트 켜기/끄기
// numberToDisplay가 10이면 모두 끄기
void lightNumber(int numberToDisplay) {

  #define SEGMENT_ON  LOW    // 세그먼트 켜기 (공통 애노드일 경우 LOW)
  #define SEGMENT_OFF HIGH   // 세그먼트 끄기

  switch (numberToDisplay){

    case 0:
      digitalWrite(segA, SEGMENT_ON);
      digitalWrite(segB, SEGMENT_ON);
      digitalWrite(segC, SEGMENT_ON);
      digitalWrite(segD, SEGMENT_ON);
      digitalWrite(segE, SEGMENT_ON);
      digitalWrite(segF, SEGMENT_ON);
      digitalWrite(segG, SEGMENT_OFF);
      break;

    case 1:
      digitalWrite(segA, SEGMENT_OFF);
      digitalWrite(segB, SEGMENT_ON);
      digitalWrite(segC, SEGMENT_ON);
      digitalWrite(segD, SEGMENT_OFF);
      digitalWrite(segE, SEGMENT_OFF);
      digitalWrite(segF, SEGMENT_OFF);
      digitalWrite(segG, SEGMENT_OFF);
      break;

    case 2:
      digitalWrite(segA, SEGMENT_ON);
      digitalWrite(segB, SEGMENT_ON);
      digitalWrite(segC, SEGMENT_OFF);
      digitalWrite(segD, SEGMENT_ON);
      digitalWrite(segE, SEGMENT_ON);
      digitalWrite(segF, SEGMENT_OFF);
      digitalWrite(segG, SEGMENT_ON);
      break;

    case 3:
      digitalWrite(segA, SEGMENT_ON);
      digitalWrite(segB, SEGMENT_ON);
      digitalWrite(segC, SEGMENT_ON);
      digitalWrite(segD, SEGMENT_ON);
      digitalWrite(segE, SEGMENT_OFF);
      digitalWrite(segF, SEGMENT_OFF);
      digitalWrite(segG, SEGMENT_ON);
      break;

    case 4:
      digitalWrite(segA, SEGMENT_OFF);
      digitalWrite(segB, SEGMENT_ON);
      digitalWrite(segC, SEGMENT_ON);
      digitalWrite(segD, SEGMENT_OFF);
      digitalWrite(segE, SEGMENT_OFF);
      digitalWrite(segF, SEGMENT_ON);
      digitalWrite(segG, SEGMENT_ON);
      break;

    case 5:
      digitalWrite(segA, SEGMENT_ON);
      digitalWrite(segB, SEGMENT_OFF);
      digitalWrite(segC, SEGMENT_ON);
      digitalWrite(segD, SEGMENT_ON);
      digitalWrite(segE, SEGMENT_OFF);
      digitalWrite(segF, SEGMENT_ON);
      digitalWrite(segG, SEGMENT_ON);
      break;

    case 6:
      digitalWrite(segA, SEGMENT_ON);
      digitalWrite(segB, SEGMENT_OFF);
      digitalWrite(segC, SEGMENT_ON);
      digitalWrite(segD, SEGMENT_ON);
      digitalWrite(segE, SEGMENT_ON);
      digitalWrite(segF, SEGMENT_ON);
      digitalWrite(segG, SEGMENT_ON);
      break;

    case 7:
      digitalWrite(segA, SEGMENT_ON);
      digitalWrite(segB, SEGMENT_ON);
      digitalWrite(segC, SEGMENT_ON);
      digitalWrite(segD, SEGMENT_OFF);
      digitalWrite(segE, SEGMENT_OFF);
      digitalWrite(segF, SEGMENT_OFF);
      digitalWrite(segG, SEGMENT_OFF);
      break;

    case 8:
      digitalWrite(segA, SEGMENT_ON);
      digitalWrite(segB, SEGMENT_ON);
      digitalWrite(segC, SEGMENT_ON);
      digitalWrite(segD, SEGMENT_ON);
      digitalWrite(segE, SEGMENT_ON);
      digitalWrite(segF, SEGMENT_ON);
      digitalWrite(segG, SEGMENT_ON);
      break;

    case 9:
      digitalWrite(segA, SEGMENT_ON);
      digitalWrite(segB, SEGMENT_ON);
      digitalWrite(segC, SEGMENT_ON);
      digitalWrite(segD, SEGMENT_ON);
      digitalWrite(segE, SEGMENT_OFF);
      digitalWrite(segF, SEGMENT_ON);
      digitalWrite(segG, SEGMENT_ON);
      break;

    case 10:  // 숫자 끄기 (모든 세그먼트 끄기)
      digitalWrite(segA, SEGMENT_OFF);
      digitalWrite(segB, SEGMENT_OFF);
      digitalWrite(segC, SEGMENT_OFF);
      digitalWrite(segD, SEGMENT_OFF);
      digitalWrite(segE, SEGMENT_OFF);
      digitalWrite(segF, SEGMENT_OFF);
      digitalWrite(segG, SEGMENT_OFF);
      break;
  }
}

```
## Bluetooth
![](./images/bluetooth.png)
```c
#include <SoftwareSerial.h> // 소프트웨어 시리얼 라이브러리 포함 (디지털 핀을 이용해 시리얼 통신 가능하게 함)

// Bluetooth 모듈과의 통신을 위해 디지털 핀 2(RX), 3(TX)를 사용하여 SoftwareSerial 객체 생성
SoftwareSerial BT(2, 3);

void setup() {
  BT.begin(9600);        // 블루투스 시리얼 통신 시작 (통신 속도: 9600bps)
  pinMode(8, OUTPUT);    // 디지털 핀 8을 출력 모드로 설정 (LED 등 외부 장치 제어용)
}

void loop() {
  // 블루투스로부터 데이터가 들어오면
  if (BT.available()) {
    int value = BT.read();  // 들어온 데이터를 읽음

    // '1'이라는 문자가 들어오면
    if (value == '1') {
      digitalWrite(8, HIGH); // 핀 8에 HIGH 신호를 출력 (LED ON 등)
    }

    // '2'라는 문자가 들어오면
    if (value == '2') {
      digitalWrite(8, LOW);  // 핀 8에 LOW 신호를 출력 (LED OFF 등)
    }
  }
}

```
## reference
+ 만들면서 배우는 아두이노와 40개의 작품들. 장문철, 앤써북
+ 스마트폰, 블루투스, 이더넷, WiFi 그리고 아두이노, 조도현 외 4, 북두출판사
+ https://wikidocs.net/30784


