//# arduino-smart-home
//this project is my arduino uno smart home's code there is a temperature sensor,flame sensor,smoke sensor and buzzer
#include <Servo.h>

#define SERVO_PIN 9
#define ALEV_PIN 10
#define GAS_PIN 6        // QAZ SENSORU D0 → D6
#define BUZZER_PIN 8
#define LED_PIN 3        // PWM LED
#define LDR_PIN A5       // fotoresistor

Servo motor;

void setup() {
  Serial.begin(9600);

  motor.attach(SERVO_PIN);
  motor.write(90);  // Başlanğıc vəziyyət → qapı bağlı

  pinMode(ALEV_PIN, INPUT_PULLUP);
  pinMode(GAS_PIN, INPUT);     // Qaz sensoru D0 rəqəmsal giriş
  pinMode(BUZZER_PIN, OUTPUT);
  pinMode(LED_PIN, OUTPUT);
}

void loop() {

  // --- Fotoresistor oxu ---
  int ldrValue = analogRead(LDR_PIN);   // 0–1023
  int ledPower = map(ldrValue, 0, 1023, 255, 0);

  // LED gücünü 2x artırırıq
  ledPower = ledPower * 2;
  if (ledPower > 255) ledPower = 255;

  analogWrite(LED_PIN, ledPower);


  // --- Serial Monitor məlumatı ---
  Serial.print("LDR: ");
  Serial.print(ldrValue);
  Serial.print("  LED Power(x2): ");
  Serial.println(ledPower);


  // --- Alev və Qaz sensor oxumaları ---
  bool alevVar = (digitalRead(ALEV_PIN) == LOW);  // LOW = Alev var
  bool gazVar = (digitalRead(GAS_PIN) == HIGH);   // D0 = HIGH → qaz var

  Serial.print("Alev: ");
  Serial.print(alevVar);
  Serial.print("  Qaz: ");
  Serial.println(gazVar);


  // --- Təhlükə varsa servo + buzzer ---
  if (alevVar || gazVar) {
    motor.write(0);        // qapı aç
    tone(BUZZER_PIN, 1000);
    delay(1000);
    noTone(BUZZER_PIN);
  } 
  else {
    motor.write(90);       // qapı bağlı
    noTone(BUZZER_PIN);
  }

  delay(150);
}
