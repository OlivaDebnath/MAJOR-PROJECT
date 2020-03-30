in this project i am going to show How To Control Home Appliances From Internet Using Arduino and ESP8266 Wifi Module. Also showing how to use ESP8266 Wifi Module and How to Call REST API from Arduino Uno and ESP8266 Wifi Module. We are showing How you can Use Arduino Uno and ESP8266 Wifi to call REST API without using any additional library and can get JSON data from Web.
Components Required :-
1 x Arduino uno board
1 x USB cable
1 x ESP8266 WiFi Module
1 x Relay Board
1 x Jumper wire (Male to Male)
9 x Jumper wire (Male to Female)
1 x Breadboard
1 x Yellow Bulb
1 x Red Bulb
Connection :-

Source Code :-
 

#include "SoftwareSerial.h"

String ssid ="WIFI_NAME";
String password="WIFI_PASSWORD";

SoftwareSerial esp(3, 2);// RX, TX

String server = "www.iotboys.com"; //Your Host 
String uri = "/YOUR_API"; // Your URI

int RED_BULB=5; 
int YELLOW_BULB=6;

void setup() {

  pinMode(RED_BULB, OUTPUT);
  pinMode(YELLOW_BULB, OUTPUT);
  
  digitalWrite(RED_BULB, HIGH);
  digitalWrite(YELLOW_BULB, HIGH);
  
  esp.begin(9600);
  
  Serial.begin(9600);

  connectWifi();
  
  httpget();
  
  delay(1000);

}
void connectWifi() {

String cmd = "AT+CWJAP=\"" +ssid+"\",\"" + password + "\"";

esp.println(cmd);

delay(4000);

if(esp.find("OK")) {

Serial.println("Connected!");

}
else {

Serial.println("Cannot connect to wifi ! Connecting again..."); }
connectWifi();

}
/////////////////////////////GET METHOD///////////////////////////////
void httpget() {
esp.println("AT+CIPSTART=\"TCP\",\"" + server + "\",80");//start a TCP connection.

if( esp.find("OK")) {

Serial.println("TCP connection ready");

} delay(1000);

String getRequest =
"GET " + uri + " HTTP/1.0\r\n" +
"Host: " + server + "\r\n" +
"Accept: *" + "/" + "*\r\n" +
"Content-Type: application/json\r\n" +
"\r\n";

String sendCmd = "AT+CIPSEND=";

esp.print(sendCmd);

esp.println(getRequest.length() );

delay(500);

if(esp.find(">")) { 
  Serial.println("Sending.."); 
  esp.print(getRequest);
  
if( esp.find("SEND OK")) { 
  
Serial.println("Packet sent");

while (esp.available()) {

String response = esp.readString();

int RED_BULB_ON = response.indexOf("RED_BULB>TRUE")>0?1:0;
int YELLOW_BULB_ON = response.indexOf("YELLOW_BULB>TRUE")>0?1:0;

if(RED_BULB_ON==1)
{
  digitalWrite(RED_BULB, LOW);
}
else
{
  digitalWrite(RED_BULB, HIGH);
}
if(YELLOW_BULB_ON==1)
{
  digitalWrite(YELLOW_BULB, LOW);
}
else
{
  digitalWrite(YELLOW_BULB, HIGH);
}
}
esp.println("AT+CIPCLOSE");

}
}
}

void loop() {
  httpget();
}