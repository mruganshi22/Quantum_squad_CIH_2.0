#include <WiFi.h>
#include <HTTPClient.h>
#include <Wire.h>
#include <LiquidCrystal_I2C.h>

// WiFi credentials
const char* ssid = "Wokwi-GUEST";
const char* password = "";

// SheetDB API URL (replace with your own SheetDB API endpoint)
const char* sheetdb_url = "https://sheetdb.io/api/v1/jsdaaqvtxap6l";

// LCD setup
LiquidCrystal_I2C lcd(0x27, 16, 2);

// LED pins
#define RED_LED       25
#define YELLOW_LED    26
#define GREEN_LED     27
#define EMERGENCY_LED 14

// Global flag
bool emergencyMode = false;

void sendDataToGoogle(String status) {
  if (WiFi.status() == WL_CONNECTED) {
    HTTPClient http;
    http.begin(sheetdb_url);
    http.addHeader("Content-Type", "application/json");

    String jsonPayload = "{\"data\":{\"status\":\"" + status + "\"}}";

    int httpResponseCode = http.POST(jsonPayload);
    Serial.print("POST Response Code: ");
    Serial.println(httpResponseCode);
    http.end();
  } else {
    Serial.println("WiFi not connected!");
  }
}

void setup() {
  Serial.begin(115200);
  delay(1000);

  lcd.init();
  lcd.backlight();

  pinMode(RED_LED, OUTPUT);
  pinMode(YELLOW_LED, OUTPUT);
  pinMode(GREEN_LED, OUTPUT);
  pinMode(EMERGENCY_LED, OUTPUT);

  WiFi.begin(ssid, password);
  Serial.print("Connecting to WiFi");

  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }

  Serial.println("\nWiFi connected.");
  Serial.println(WiFi.localIP());

  sendDataToGoogle("System Booted");
}

void loop() {
  if (emergencyMode) {
    digitalWrite(RED_LED, LOW);
    digitalWrite(YELLOW_LED, LOW);
    digitalWrite(GREEN_LED, LOW);
    digitalWrite(EMERGENCY_LED, HIGH);

    lcd.setCursor(0, 0);
    lcd.print("  EMERGENCY MODE ");
    lcd.setCursor(0, 1);
    lcd.print("   EMERGENCY     ");
    sendDataToGoogle("EMERGENCY");
    delay(5000);
  } else {
    digitalWrite(EMERGENCY_LED, LOW);

    // RED
    digitalWrite(RED_LED, HIGH);
    digitalWrite(YELLOW_LED, LOW);
    digitalWrite(GREEN_LED, LOW);
    lcd.setCursor(0, 0);
    lcd.print(" TRAFFIC SIGNAL  ");
    lcd.setCursor(0, 1);
    lcd.print("      STOP       ");
    sendDataToGoogle("STOP");
    delay(3000);

    // YELLOW
    digitalWrite(RED_LED, LOW);
    digitalWrite(YELLOW_LED, HIGH);
    digitalWrite(GREEN_LED, LOW);
    lcd.setCursor(0, 1);
    lcd.print("     READY       ");
    sendDataToGoogle("READY");
    delay(1500);

    // GREEN
    digitalWrite(RED_LED, LOW);
    digitalWrite(YELLOW_LED, LOW);
    digitalWrite(GREEN_LED, HIGH);
    lcd.setCursor(0, 1);
    lcd.print("      GO         ");
    sendDataToGoogle("GO");
    delay(3000);
  }

  // For demo: Toggle emergency every loop
  emergencyMode = !emergencyMode;
}
