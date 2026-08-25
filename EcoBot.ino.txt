 



/*

  ==========================

        == EcoBot ==

  ==========================

*/



#include <Bluepad32.h>

#include <Wire.h>

#include <Adafruit_PWMServoDriver.h>



Adafruit_PWMServoDriver pwm = Adafruit_PWMServoDriver();



// إعداد السيرفو

#define SERVO_FREQ 50

#define SERVO_MIN 150

#define SERVO_MAX 600



// بنات الموتور (ESP32)

#define ENA 16

#define IN1 17

#define IN2 18

#define ENB 25

#define IN3 26

#define IN4 27



// قنوات PWM

#define CH_A 0

#define CH_B 1



// قنوات السيرفو

#define BASE 12

#define SHOULDER 13

#define ELBOW 14

#define GRIPPER 15



#define SDA_PIN 4

#define SCL_PIN 15



// زوايا الذراع

int baseA = 180, shoulderA = 90, elbowA = 90, gripperA = 90;



// أوامر الحركة

#define FWD 1

#define BWD 2

#define STOP 3



ControllerPtr pad[BP32_MAX_GAMEPADS];



// ===== الموتور =====

void motor(bool left, uint8_t dir, uint8_t spd) {

  uint8_t inA, inB, ch;

  if (left) { inA = IN1; inB = IN2; ch = CH_A; }

  else { inA = IN3; inB = IN4; ch = CH_B; }



  switch (dir) {

    case FWD: digitalWrite(inA, HIGH); digitalWrite(inB, LOW); break;

    case BWD: digitalWrite(inA, LOW);  digitalWrite(inB, HIGH); break;

    default:  digitalWrite(inA, LOW);  digitalWrite(inB, LOW); break;

  }



  ledcWrite(ch, (dir == STOP || spd == 0) ? 0 : 255);

}



// ===== السيرفو =====

void servo(uint8_t ch, int ang) {

  int pulse = map(ang, 0, 180, SERVO_MIN, SERVO_MAX);

  pwm.setPWM(ch, 0, pulse);

}



// ===== البلوتوث =====

void onConnect(ControllerPtr ctl) {

  for (int i = 0; i < BP32_MAX_GAMEPADS; i++)

    if (!pad[i]) { pad[i] = ctl; Serial.println("🎮 متصل!"); return; }

}

void onDisconnect(ControllerPtr ctl) {

  for (int i = 0; i < BP32_MAX_GAMEPADS; i++)

    if (pad[i] == ctl) { pad[i] = nullptr; Serial.println("🚫 انفصل!"); motor(true, STOP, 0); motor(false, STOP, 0); return; }

}



// ===== التحكم =====

void control(ControllerPtr ctl) {

  int ly = ctl->axisY();

  int move = map(ly, -508, 512, 255, -255);

  int turn = 0;

  const int TURN_SPD = 255;



  // 🔹 يمين = دائرة | يسار = مثلث

  if (ctl->buttons() & BUTTON_B) turn = TURN_SPD;

  else if (ctl->buttons() & BUTTON_Y) turn = -TURN_SPD;



  int L = constrain(move + turn, -255, 255);

  int R = constrain(move - turn, -255, 255);

  const int DEAD = 30;



  if (L > DEAD) motor(true, FWD, L);

  else if (L < -DEAD) motor(true, BWD, -L);

  else motor(true, STOP, 0);



  if (R > DEAD) motor(false, FWD, R);

  else if (R < -DEAD) motor(false, BWD, -R);

  else motor(false, STOP, 0);



  // 🔹 الذراع

  int ry = ctl->axisRY();

  const int DZ = 30, rate = 1;



  if (ctl->buttons() & BUTTON_TRIGGER_R) baseA = constrain(baseA - rate, 0, 180);

  else if (ctl->buttons() & BUTTON_TRIGGER_L) baseA = constrain(baseA + rate, 0, 180);



  if (ry < -DZ) shoulderA = constrain(shoulderA + rate, 0, 140);

  else if (ry > DZ) shoulderA = constrain(shoulderA - rate, 0, 140);



  if (ctl->buttons() & BUTTON_SHOULDER_R) elbowA = constrain(elbowA + rate, 0, 130);

  else if (ctl->buttons() & BUTTON_SHOULDER_L) elbowA = constrain(elbowA - rate, 0, 130);



  if (ctl->buttons() & BUTTON_A) gripperA = 10;

  else if (ctl->buttons() & BUTTON_X) gripperA = 90;



  servo(BASE, baseA);

  servo(SHOULDER, shoulderA);

  servo(ELBOW, elbowA);

  servo(GRIPPER, gripperA);

}



// ===== الإعداد =====

void setup() {

  Serial.begin(115200);

  Serial.println("EcoBot جاهز ✅");



  pinMode(IN1, OUTPUT); pinMode(IN2, OUTPUT);

  pinMode(IN3, OUTPUT); pinMode(IN4, OUTPUT);

  pinMode(ENA, OUTPUT); pinMode(ENB, OUTPUT);



  digitalWrite(IN1, LOW); digitalWrite(IN2, LOW);

  digitalWrite(IN3, LOW); digitalWrite(IN4, LOW);



  ledcSetup(CH_A, 5000, 8); ledcSetup(CH_B, 5000, 8);

  ledcAttachPin(ENA, CH_A); ledcAttachPin(ENB, CH_B);

  ledcWrite(CH_A, 0); ledcWrite(CH_B, 0);



  Wire.begin(SDA_PIN, SCL_PIN);

  pwm.begin(); pwm.setOscillatorFrequency(27000000);

  pwm.setPWMFreq(SERVO_FREQ); delay(10);



  servo(BASE, baseA); servo(SHOULDER, shoulderA);

  servo(ELBOW, elbowA); servo(GRIPPER, gripperA);



  BP32.setup(&onConnect, &onDisconnect);

  BP32.enableVirtualDevice(true);

  BP32.forgetBluetoothKeys();



  Serial.println("🎮 استنى الكنترولر...");

}



// ===== التشغيل =====

void loop() {

  BP32.update();

  bool active = false;

  for (auto ctl : pad)

    if (ctl && ctl->isConnected()) { control(ctl); active = true; }



  vTaskDelay(active ? 1 : 5);

}

