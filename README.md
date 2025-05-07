#include <Wire.h> 
#include <LiquidCrystal_I2C.h> 

#define PIR_PIN_MASUK 2  
#define PIR_PIN_KELUAR 3 
#define RESET_BUTTON_PIN 4
#define LED_PIN 13 

int countMasuk = 0;  
int countKeluar = 0;  
bool prevMotionDetectedMasuk = false; 
bool prevMotionDetectedKeluar = false; 

LiquidCrystal_I2C lcd(0x27, 16, 2); 

void setup() {
  pinMode(PIR_PIN_MASUK, INPUT);  
  pinMode(PIR_PIN_KELUAR, INPUT); 
  pinMode(RESET_BUTTON_PIN, INPUT_PULLUP);
  pinMode(LED_PIN, OUTPUT); 
  digitalWrite(LED_PIN, LOW);
  
  lcd.init();
  lcd.backlight(); 
  lcd.clear();
  lcd.setCursor(0, 0); 
  lcd.print("Masuk: ");
  lcd.setCursor(0, 1);
  lcd.print("Keluar: ");
}

void loop() {
  bool motionDetectedMasuk = digitalRead(PIR_PIN_MASUK); 
  bool motionDetectedKeluar = digitalRead(PIR_PIN_KELUAR); 
  bool resetButtonState = digitalRead(RESET_BUTTON_PIN);

  if (resetButtonState == LOW) {
    resetCounts(); 
  }

  if (motionDetectedMasuk && !prevMotionDetectedMasuk) {
    countMasuk++; 
    updateLCD();
    digitalWrite(LED_PIN, HIGH);
    delay(1000);
    digitalWrite(LED_PIN, LOW);
  } else if (motionDetectedKeluar && !prevMotionDetectedKeluar) {
    countKeluar++;
    updateLCD(); 
    digitalWrite(LED_PIN, HIGH);
    delay(1000);
    digitalWrite(LED_PIN, LOW);
  }

  prevMotionDetectedMasuk = motionDetectedMasuk;
  prevMotionDetectedKeluar = motionDetectedKeluar;
}

void updateLCD() {
  lcd.setCursor(7, 0); 
  lcd.print("   "); 
  lcd.setCursor(7, 0);
  lcd.print(countMasuk);
  lcd.setCursor(7, 1); 
  lcd.print("   "); 
  lcd.setCursor(7, 1); 
  lcd.print(countKeluar); 
}

void resetCounts() {
  countMasuk = 0; 
  countKeluar = 0; 
  updateLCD(); 
  delay(1000); 
}
