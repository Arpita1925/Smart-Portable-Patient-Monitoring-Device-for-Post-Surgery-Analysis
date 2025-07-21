

#define BLYNK_TEMPLATE_ID "TMPL3aTtfwqeE"
#define BLYNK_TEMPLATE_NAME "Remote Helath Monitor"
#define BLYNK_AUTH_TOKEN "CAsCVZROxJjJKkkHJWyHuZSH0GRm2Iql"


#include <ESP8266WiFi.h>
#include <BlynkSimpleEsp8266.h>
#include <DHT.h>

char ssid[] = "pcetstaff11";
char pass[] = "coffin19";

#define PULSE_PIN A0
#define DHTPIN D4
#define DHTTYPE DHT11

DHT dht(DHTPIN, DHTTYPE);
BlynkTimer timer;

int threshold = 512;
int lastValue = 0;
unsigned long lastBeatTime = 0;
int bpm = 0;

void checkPulse() {
  int value = analogRead(PULSE_PIN);

  if (value > threshold && lastValue <= threshold) {
    unsigned long currentTime = millis();
    unsigned long beatInterval = currentTime - lastBeatTime;

    if (beatInterval > 300) {
      bpm = 60000 / beatInterval;
      lastBeatTime = currentTime;

      Serial.print("❤️ Heartbeat detected! BPM: ");
      Serial.println(bpm);

      Blynk.virtualWrite(V0, bpm);
    }
  }

  lastValue = value;
}

void sendTemperature() {
  float temp = dht.readTemperature();

  if (isnan(temp)) {
    Serial.println("❌ Failed to read from DHT sensor!");
    return;
  }

  Serial.print("🌡 Temperature: ");
  Serial.print(temp);
  Serial.println("°C");

  Blynk.virtualWrite(V1, temp);

  if (Blynk.connected()) {
    Serial.println("✅ Sent temperature to Blynk.");
  } else {
    Serial.println("❌ Not connected to Blynk.");
  }
}

void setup() {
  Serial.begin(115200);
  Blynk.begin(BLYNK_AUTH_TOKEN, ssid, pass);
  dht.begin();

  // Set timers for both pulse and temperature
  timer.setInterval(60L, checkPulse);       // 50 times per second
  timer.setInterval(6000L, sendTemperature); // Every 2 seconds
}

void loop() {
  Blynk.run();
  timer.run();
}
