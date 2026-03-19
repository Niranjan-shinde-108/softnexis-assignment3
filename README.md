# softnexis-assignment3
This project demonstrates IoT connectivity using an ESP32. The device connects to a WiFi network and communicates with a cloud server using HTTP requests. A GET request is used to fetch real-time data from a public API, showcasing how IoT devices interact with the internet.

#include <LiquidCrystal_I2C.h>
#define BLYNK_PRINT Serial
#include <ESP8266WiFi.h>
#include <BlynkSimpleEsp8266.h>
#include <DHT.h>

LiquidCrystal_I2C lcd(0x27, 16, 2);
DHT dht(D3, DHT11); //(sensor pin,sensor type)
BlynkTimer timer;

char auth[] = ""; //Enter the Auth code which was send by Blink
char ssid[] = "";  //Enter your WIFI Name
char pass[] = "";  //Enter your WIFI Password

void weather() {
  float h = dht.readHumidity();
  float t = dht.readTemperature();
  int r = analogRead(A0);
  bool l = digitalRead(D4);

  r = map(r, 0, 1023, 100, 0);
  if (isnan(h) || isnan(t)) {
    Serial.println("Failed to read from DHT sensor!");
    return;
  }

  Blynk.virtualWrite(V0, t);  //V0 is for Temperature
  Blynk.virtualWrite(V1, h);  //V1 is for Humidity
  Blynk.virtualWrite(V2, r);  //V2 is for Rainfall

  if (l == 0) {
    WidgetLED led1(V3);
    led1.on();
    lcd.setCursor(9, 1);
    lcd.print("L :");
    lcd.print("High");
    lcd.print(" ");
  } else if (l == 1) {
    WidgetLED led1(V3);
    led1.off();
    lcd.setCursor(9, 1);
    lcd.print("L :");
    lcd.print("Low");
    lcd.print(" ");
  }

  lcd.setCursor(0, 0);
  lcd.print("T :");
  lcd.print(t);

  lcd.setCursor(0, 1);
  lcd.print("H :");
  lcd.print(h);

  lcd.setCursor(9, 0);
  lcd.print("R :");
  lcd.print(r);
  lcd.print("  ");

}

void setup() {
  Serial.begin(9600); // See the connection status in Serial Monitor
  lcd.init();
  lcd.backlight();
  Blynk.begin(auth, ssid, pass);
  dht.begin();
  // Setup a function to be called every second
  timer.setInterval(10L, weather);
}

void loop() {
  Blynk.run(); // Initiates Blynk
  timer.run(); // Initiates SimpleTimer
}

Code explanation
Firstly, library files are included.

#include <LiquidCrystal_I2C.h>
#define BLYNK_PRINT Serial
#include <ESP8266WiFi.h>
#include <BlynkSimpleEsp8266.h>
#include <DHT.h>

Next, objects are created for these libraries.

LiquidCrystal_I2C lcd(0x27, 16, 2); //I2C address, LCD length, and width
DHT dht(D3, DHT11); //(sensor pin,sensor type)
BlynkTimer timer;

OK, now let’s include the Blynk app Auth token and Wi-Fi connection details.






char auth[] = "VEilfTlAm3vxyrTMCNwrE8VA695ElEv8"; //Enter the Auth code which was send by Blink
char ssid[] = "Dialog 4G 025";  //Enter your WIFI Name
char pass[] = "b2016ee3";  //Enter your WIFI Password

This is the main function of this program,

void weather() {
  float h = dht.readHumidity(); //Gets the humidity value through the DHT11 sensor
  float t = dht.readTemperature();//Gets the temperature value through the DHT11 sensor
  int r = analogRead(A0);//Gets the rainfall value through the rain sensor
  bool l = digitalRead(D4);//Gets the light value through the LDR sensor

  r = map(r, 0, 1023, 100, 0);//Converts the rainfall value from 0 to 100
//The functionality of the DHT11 sensor is checked
  if (isnan(h) || isnan(t)) {
    Serial.println("Failed to read from DHT sensor!");
    return;
  }
//Sensor values are sent to the Blynk app
  Blynk.virtualWrite(V0, t);  //V0 is for Temperature
  Blynk.virtualWrite(V1, h);  //V1 is for Humidity
  Blynk.virtualWrite(V2, r);  //V2 is for Rainfall
//LDR sensor values are checked using the IF condition. If the value is 0, the LED widget is light up
  if (l == 0) {
    WidgetLED led1(V3);
    led1.on();
    lcd.setCursor(9, 1);
    lcd.print("L :");
    lcd.print("High");
    lcd.print(" ");
//If the value is 1, the LED widget is dimmed
  } else if (l == 1) {
    WidgetLED led1(V3);
    led1.off();
    lcd.setCursor(9, 1);
    lcd.print("L :");
    lcd.print("Low");
    lcd.print(" ");
  }
//Sensor values are printed on the LCD
  lcd.setCursor(0, 0);
  lcd.print("T :");
  lcd.print(t);

  lcd.setCursor(0, 1);
  lcd.print("H :");
  lcd.print(h);

  lcd.setCursor(9, 0);
  lcd.print("R :");
  lcd.print(r);
  lcd.print("  ");

}

In the setup function,

void setup() {
  Serial.begin(9600); // The serial monitor is beginning
//The LCD, Blynk, and DHT11 libraries are starts
  lcd.init();
  lcd.backlight();
  Blynk.begin(auth, ssid, pass);
  dht.begin();
  // This code the main function is called
  timer.setInterval(10L, weather);
}

In the loop function, the Blynk library is run.

void loop() {
  Blynk.run(); // Initiates Blynk
  timer.run(); // Initiates SimpleTimer
}
