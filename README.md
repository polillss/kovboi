int d1 = 1000;   // d1 и d2 - диапазон включения светодиода
int d2 = 2000;
int d3 = 3000;   // через сколько сек будет засчитана ошибка
byte tarrgr = 5; // количество попыток
byte knp = 3;    // количество пар кнопок/светодиодов
 
byte pinD = 4 + knp; 
byte pinA = 14 + knp;
byte ext = 9;
byte hit;
byte err;
byte tarrgn;
boolean finich = true;

unsigned long timing; 
unsigned long setreakt; 
unsigned long bestreakt = 99999;
unsigned long medreakt;

void setup() {
   Serial.begin(9600);
   for (byte i = 4; i < pinD; i++) { pinMode(i, OUTPUT); }
   for (byte i = 14; i < pinA; i++) { pinMode(i, INPUT); }
   for (byte i = 14; i < pinA; i++) { analogWrite(i, LOW); }
}

void loop() {
   while (tarrgn < tarrgr) {
    
      int LED = random(4, 4 + knp);
      int BTN = LED + 10;

      int DEL = random(d1, d2);
      delay(DEL);
      digitalWrite(LED, HIGH);
      timing = millis();

      ext = 9;

      while (ext < 10 && tarrgn < tarrgr) {
         if (digitalRead(BTN) == HIGH) {
            digitalWrite(LED, LOW);
            setreakt = millis() - timing;
            delay(300); hit++; tarrgn++; ext = 11;
         }
         if (millis() - timing > d3) {
            digitalWrite(LED, LOW);
            delay(300);
            setreakt = 0; err++; tarrgn++; ext = 11;
         }
      }

    if (setreakt < bestreakt && setreakt > 100) { bestreakt = setreakt; }
    medreakt = medreakt + setreakt;
  }

    if (tarrgn == tarrgr && finich == true) {
       medreakt = medreakt / hit;
       if (medreakt > 99998) { medreakt = 0; }
       if (bestreakt > 99998) { bestreakt = 0; }

       Serial.println();
       Serial.println("--- End of Round ---");
       Serial.println();
       Serial.println("Hits - " + String(hit));
       Serial.println("Errors - " + String(err));
       Serial.println("Best reaction - " + String(bestreakt));
       Serial.println("Medium reaction - " + String(medreakt));
       Serial.println();
       Serial.println("Reboot for restart");
       finich = false;
  }
}
