// ESP8266 ต่อกับ SHT21 เซ็นเซอร์วัดอุณหภูมความชื้น เพื่อแสดงผลกับ จอ Display 1.3" OLED
// การ wiring SDA==>D2 และ SCL==>D1 ทั้ง จอและ sensor
#include <Arduino.h>
#include <U8g2lib.h>

#ifdef U8X8_HAVE_HW_SPI
#include <SPI.h>
#endif
#ifdef U8X8_HAVE_HW_I2C
#include <Wire.h>
#endif

#include "Wire.h"
#include "SHT2x.h"

uint32_t start;
uint32_t stop;

SHT2x sht;

U8G2_SSD1306_128X64_NONAME_1_HW_I2C u8g2(U8G2_R0, /* reset=*/ U8X8_PIN_NONE);

void setup()
{
  Serial.begin(115200);
  Serial.println(__FILE__);
  Serial.print("SHT2x_LIB_VERSION: \t");
  Serial.println(SHT2x_LIB_VERSION);
  u8g2.begin();  
  sht.begin();

  uint8_t stat = sht.getStatus();
  Serial.print(stat, HEX);
  Serial.println();
}

void loop()
{
    start = micros();
  sht.read();
  stop = micros();

  Serial.print("\t");
  Serial.print(stop - start);
  Serial.print("\t");
  Serial.print(sht.getTemperature(), 1);
  Serial.print("\t");
  Serial.println(sht.getHumidity(), 1);
  delay(1000);
  
  u8g2.firstPage();
  do {
    u8g2.setFont(u8g2_font_ncenB12_tr);
    //u8g2.drawStr(0,24,"Hello World!");
    u8g2.setCursor(5, 24);
    u8g2.print(sht.getTemperature(), 1);
    u8g2.setCursor(5, 55);
    u8g2.print(sht.getHumidity(), 1);
  } while ( u8g2.nextPage() );

}
