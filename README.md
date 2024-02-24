# AQI-MONITOR
AQI MONITOR USING ARDUINO, SDS011 (FOR PM2.5 &amp; PM10), MQ135 AND DHT11 

HERE'S A GUIDE TO MAKE AN AQI SENSOR USING THE ABOVE MENTIONED SENSORS

[Here's a video of the running model](https://imgur.com/a/benoDAu)

![final](https://github.com/dakumra/AQI-MONITOR/assets/95081306/ac739c8a-9814-439e-ad01-455ba636fd48)

This is the link to connect an SPI display if you use one, please see if you have a different interface 
https://www.elecrow.com/wiki/index.php?title=1.44%27%27_128x_128_TFT_LCD_with_SPI_Interface

## THE ARDUINO CODE

```
#include <SPI.h>
#include <Adafruit_GFX.h>
#include <TFT_ILI9163C.h>
#include <DHT.h>
#include <MQ135.h>
#include "SDS011.h"

// TFT settings
#define __DC 9
#define __CS 10
#define __RST 12

// DHT settings
#define DHT_PIN 7 // Replace with the actual pin number to which DHT data pin is connected
#define DHT_TYPE DHT11
DHT dht(DHT_PIN, DHT_TYPE);

// MQ135 settings
#define MQ135_PIN A0 // Replace with the actual analog pin number to which MQ135 analog pin is connected
MQ135 mq135(MQ135_PIN);

// SDS011 settings
float p10, p25;
int error;
SDS011 my_sds;


// Color definitions
#define BLACK   0x0000
#define BLUE    0x001F
#define RED     0xF800
#define GREEN   0x07E0
#define CYAN    0x07FF
#define MAGENTA 0xF81F
#define YELLOW  0xFFE0  
#define WHITE   0xFFFF

TFT_ILI9163C tft = TFT_ILI9163C(__CS, __DC, __RST);

void setup() {
  Serial.begin(9600);

  // Initialize DHT sensor
  dht.begin();

  // Initialize SDS011 sensor
  my_sds.begin(2, 3);

  // Initialize TFT display
  tft.begin();
}

void loop() {
  // Read DHT11 values
  float temperature = dht.readTemperature();
  float humidity = dht.readHumidity();

  // Read MQ135 values
  float co2 = mq135.getPPM();
 // float methane = mq135.getRZero();

  // Read SDS011 values
  error = my_sds.read(&p25, &p10);

  // Clear the screen
  tft.fillScreen(BLACK);

  // Display DHT11 values
  tft.setTextSize(1);
  tft.setTextColor(WHITE);
  tft.setCursor(10, 10);
  tft.print("Temperature: ");
  tft.print(temperature);
  tft.print(" C  ");

  tft.setCursor(10, 30);
  tft.print("Humidity: ");
  tft.print(humidity);
  tft.println(" %");

  // Display MQ135 values
  tft.setCursor(10, 50);
  tft.print("CO2: ");
  tft.print(co2);
  tft.print(" ppm  ");

  //tft.print("Methane: ");
  //tft.println(methane);

  // Display SDS011 values
  if (!error) {
    tft.setCursor(10, 70);
    tft.print("P2.5: ");
    tft.print(p25);
    tft.print(" µg/m³  ");

    tft.print("P10: ");
    tft.print(p10);
    tft.println(" µg/m³");
  } else {
    tft.setCursor(10, 70);
    tft.println("Error reading from SDS011 sensor");
  }
  if (p10 > p25) {
    tft.setCursor(15, 90);
    tft.println("Overall aqi");
    tft.print(p10);
  }
  else{
    tft.setCursor(15, 90);
    tft.println("Overall aqi");
    tft.print(p25);

}
  delay(2000); // Adjust the delay as needed
}
```

