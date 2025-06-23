# sensor예제
## DC Motor
![](./images/DCmotor.png)
```c
void setup() 
{
  pinMode(9, OUTPUT);
}
void loop()
{
  Serial.begin(9600);
  int inputValue = analogRead(A0);
  Serial.println(inputValue);
  int convertedValue = map(inputValue, 0, 1023, 0, 255);

  analogWrite(9, convertedValue);
  delay(100);
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
const int MOTOR_PIN_A = 5;
const int MOTOR_PIN_B = 6;

void setup() 
{
  pinMode(MOTOR_PIN_A, OUTPUT);
  pinMode(MOTOR_PIN_B, OUTPUT);
}
void loop()
{
  int readValue = digitalRead(4);

  if(readValue == LOW) {	
    analogWrite(MOTOR_PIN_A, 255);
    analogWrite(MOTOR_PIN_B, 0);   // B측은 0 → 모터 한 방향 회전
  }
  else {
    analogWrite(MOTOR_PIN_A, 0);
    analogWrite(MOTOR_PIN_B, 255);  //반대방향
  }   
  delay(100);
}
```
![](./images/DCmotor4.png)
```c
// Motor A connections
int enA = 9;
int in1 = 8;
int in2 = 7;
// Motor B connections
int enB = 3;
int in3 = 5;
int in4 = 4;

void setup() {
	// Set all the motor control pins to outputs
	pinMode(enA, OUTPUT);
	pinMode(enB, OUTPUT);
	pinMode(in1, OUTPUT);
	pinMode(in2, OUTPUT);
	pinMode(in3, OUTPUT);
	pinMode(in4, OUTPUT);
	
	// Turn off motors - Initial state
	digitalWrite(in1, LOW);
	digitalWrite(in2, LOW);
	digitalWrite(in3, LOW);
	digitalWrite(in4, LOW);
}

void loop() {
	directionControl();
	delay(1000);
	speedControl();
	delay(1000);
}

// This function lets you control spinning direction of motors
void directionControl() {
	// Set motors to maximum speed
	// For PWM maximum possible values are 0 to 255
	analogWrite(enA, 255);
	analogWrite(enB, 255);

	// Turn on motor A & B
	digitalWrite(in1, HIGH);
	digitalWrite(in2, LOW);
	digitalWrite(in3, HIGH);
	digitalWrite(in4, LOW);
	delay(2000);
	
	// Now change motor directions
	digitalWrite(in1, LOW);
	digitalWrite(in2, HIGH);
	digitalWrite(in3, LOW);
	digitalWrite(in4, HIGH);
	delay(2000);
	
	// Turn off motors
	digitalWrite(in1, LOW);
	digitalWrite(in2, LOW);
	digitalWrite(in3, LOW);
	digitalWrite(in4, LOW);
}

// This function lets you control speed of the motors
void speedControl() {
	// Turn on motors 방향설정
	digitalWrite(in1, LOW);
	digitalWrite(in2, HIGH);
	digitalWrite(in3, LOW);
	digitalWrite(in4, HIGH);
	
	// 가속
	for (int i = 0; i < 256; i++) {
		analogWrite(enA, i);
		analogWrite(enB, i);
		delay(20);
	}
	
	// 감속
	for (int i = 255; i >= 0; --i) {
		analogWrite(enA, i);
		analogWrite(enB, i);
		delay(20);
	}
	
	// 정지
	digitalWrite(in1, LOW);
	digitalWrite(in2, LOW);
	digitalWrite(in3, LOW);
	digitalWrite(in4, LOW);
}
```
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
