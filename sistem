//Library
#include <LiquidCrystal_I2C.h>
#include <EEPROM.h>
#include "GravityTDS.h"
#include <Servo.h>
#include <SoftwareSerial.h>

SoftwareSerial mySerial(2,3);

//deklarasi pin
#define echoPin 4
#define trigPin 5
#define echoPin1 6
#define trigPin1 7
#define echoPin2 8
#define trigPin2 9
#define servoPin1 10
#define servoPin2 11
#define servoPin3 12
Servo servo1, servo2, servo3;

//LCD, TDS
LiquidCrystal_I2C lcd(0x27, 20, 4);
#define TdsSensorPin A1
GravityTDS gravityTds;

//Ph
const int analogInPin = A0; 
int sensorValue = 0; 
unsigned long int avgValue; 
float b;
int buf[10],temp=0;

int batasppm = 1500; // tanaman pokchoy
int pokcoy = 1050;
String tumbuhan= "";
String data= "";
float temperature = 25,tdsValue = 0;

//Variabel Ultrasonik
long waktu, waktu1, waktu2;
int jarak, jarak1, jarak2;

void setup() {
  servo1.attach(servoPin1);
  servo2.attach(servoPin2);
  servo3.attach(servoPin3);
  
  //menetapkan posisi awal servo  
  servo1.write(0);
  servo2.write(0);
  servo3.write(0);  

  //memulai Serial dan LCD
  Serial.begin(115200);
  mySerial.begin(115200);
  lcd.begin();
  lcd.backlight();

  //Pemberian perintah untuk pin ultrasonik
  pinMode(trigPin,OUTPUT);
  pinMode(echoPin,INPUT);
  pinMode(trigPin1,OUTPUT);
  pinMode(echoPin1,INPUT);
  pinMode(trigPin2,OUTPUT);
  pinMode(echoPin2,INPUT);

  //Menampilkan Pada LCD "Hidroponik Monitoring selama 3 detik"
  lcd.setCursor(5,1);
  lcd.print("Hidroponik");
  lcd.setCursor(5,2);
  lcd.print("Monitoring");
  delay(3000);

  //Menghapus LCD dan Menampilkan deffault monitoring
  lcd.clear();    
  lcd.setCursor(0,0);
  lcd.print("Tinggi : ");
  lcd.setCursor(0,1);
  lcd.print("PPM    : ");    
  lcd.setCursor(0,2);    
  lcd.print("Ph     : ");  

  //Penyiapan Modul TDS
  gravityTds.setPin(TdsSensorPin);
  gravityTds.setAref(5.0);  //reference voltage on ADC, default 5.0V on Arduino UNO
  gravityTds.setAdcRange(1024);  //1024 for 10bit ADC;4096 for 12bit ADC
  gravityTds.begin();  //initialization
  }
  
  void loop (){
    //
    digitalWrite(trigPin,LOW);
    digitalWrite(trigPin1,LOW);
    digitalWrite(trigPin2,LOW);
    delay(200);
    digitalWrite(trigPin,HIGH);
    digitalWrite(trigPin1,HIGH);
    digitalWrite(trigPin2,HIGH);
    delay(500);
    digitalWrite(trigPin,LOW);
    digitalWrite(trigPin1,LOW);
    digitalWrite(trigPin2,LOW);
  
    gravityTds.setTemperature(temperature);  // set the temperature and execute temperature compensation
    gravityTds.update();  //sample and calculate 
    tdsValue = gravityTds.getTdsValue();  // then get the value

    //perhitungan ultrasonik  
    waktu=pulseIn(echoPin,HIGH);
    jarak = (waktu * 0.0345)/2;
    waktu1=pulseIn(echoPin1,HIGH);
    jarak1 = (waktu1 * 0.0345)/2;
    waktu2=pulseIn(echoPin2,HIGH);
    jarak2 = (waktu2 * 0.0345)/2;
    
    //Perhitungan Ph yang memusingkan
    for(int i=0;i<10;i++) {
    buf[i]=analogRead(analogInPin);
    delay(10);
    } for(int i=0;i<9;i++) {
      for(int j=i+1;j<10;j++) {
        if(buf[i]>buf[j]) {
          temp=buf[i];
          buf[i]=buf[j];
          buf[j]=temp;
          }
        }
      }
      avgValue=0;
      for(int i=2;i<8;i++)
      avgValue+=buf[i];
      float pHVol=(float)avgValue*5.0/1024/4.3;
      float phValue = -5.70 * pHVol + 29.5 ;
      phValue=14.2-phValue;

    lcd.setCursor(9,0);
    lcd.print("        ");
    lcd.setCursor(9,0);
    lcd.print(jarak);
    lcd.print(" cm");
       
    lcd.setCursor(9,1);    
    lcd.print(tdsValue);
    
    lcd.setCursor(9,2);    
    lcd.print(phValue);    

    if (jarak>10) {
    servo1.write(90);
    } else {    
    servo1.write(0);  
    }
    
    if (tdsValue<batasppm) {
      servo2.write(0);
      servo3.write(0);
    } else {
      servo2.write(90);
      servo3.write(90);
    }
      // put your main code here, to run repeatedly:
  while (mySerial.available() > 0) {
    char c = mySerial.read();
    data += c;
  }
  if (data.length() > 0){
    Serial.println("data yang masuk : " + data);
    if (data == "cekppm") {
      mySerial.print("PPM : " + String(tdsValue));
    }
    else if (data == "setppm pokhcoy") {
      batasppm = pokcoy;
      tumbuhan = "pokcoy";
  }
    else if (data == "cekph") {
    mySerial.print("Ph : " + String(phValue));
  }
    else if(data == "air utama") {
      mySerial.print("jarak air : " + String(jarak));
  }
    else if (data == "tumbuhan") {
      mySerial.print("Tumbuhan yang dipakai : " + String(tumbuhan));
  }
      else if (data == "STATUS") {
    mySerial.print("AirUtama : " + String(jarak) + "cm" + '#' 
                 + "PPM      : " + String(tdsValue) + '#'
                 + "Ph       : " + String(phValue)  + '#'
                 + "Protein dan Nutrisi : " + String(jarak1) + "cm" + " dan " + String(jarak2) + "cm" + '#'
                 + "Tumbuhan yang dipakai :"  + String(tumbuhan));
 }
  data="";
}
}
