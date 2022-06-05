# Pilseviyedevresi

## MARMARA ÜNİVERSİTESİ TEKNOLOJİ FAKULTESİ Elektrik Elektronik Mühendisliği 
### Ölçme ve Enstrümantasyon DERSİ LabVIEW Proje (Batarya Ölçme Devresi) Hazırlayan : Sinan Sayhan  / 171519011
### Kullanılan Malzemeler
- Esp32 
- 3.2K Direnç 
- 1k Direnç 

### Devrenin tasarımı
**Esp üzerinden gerilim bölücü yardımıyla analog veriyi 16 dan  
3.3V’a indirgeyerek Esp içerisinde Tablo-1 deki karakteristiği baz alınarak If komutu LabWiev yazdırılmıştır.**

[LabWiev Dosyalarına Erişim Linki](https://drive.google.com/drive/folders/1sfm7uOUn1OdS-ZRUzJ6rGv0hdDpRFc0G?usp=sharing)
![image](https://user-images.githubusercontent.com/97916376/172055786-b04ab085-895a-4f64-8249-795f5a4a7685.png)


![image](https://user-images.githubusercontent.com/97916376/172052599-edad374c-dd13-4949-9469-8e60bd0f1e57.png)


### Kaynaktan Verilen Max Gerilim

![image](https://user-images.githubusercontent.com/97916376/172052976-a700a809-fea3-491b-8a50-a0ec030f47e5.png)
### Gerilim Bölücüden Alınan Max Gerilim**

![image](https://user-images.githubusercontent.com/97916376/172052996-86795c43-b0b6-4056-8a7b-a9bf7717f2e0.png)

### Verilen Gerilim ile oluşan Kontol Panelinin Davranışları

![image](https://user-images.githubusercontent.com/97916376/172053181-1fc2d980-88a4-433c-b684-3a54463e0d14.png)

![image](https://user-images.githubusercontent.com/97916376/172053241-c1b98343-2c85-41f5-b23c-0fc52b6740cd.png)

![image](https://user-images.githubusercontent.com/97916376/172053262-423d4c72-ec02-4b7d-afb4-8cf3057be824.png)

![image](https://user-images.githubusercontent.com/97916376/172053277-f3236227-793b-4d09-a5b8-eeb6e7265c94.png)



```
#include <WiFi.h>
#include <WiFiClient.h>
#include "ThingSpeak.h"
#define pin 34
char id[] = "SinanSayhn";
char sifre[] = "122sayxx";
WiFiClient  client;
unsigned long myChannelNumber = 1;
const char * myWriteAPIKey = "6HTKLFBWOH7XSA1V";
unsigned long sonyazma = 0;
unsigned long gecikme = 30000;

float g =0 , gy ;  // Değişkenler tanımlandı

float writedata;

void setup() {
  Serial.begin(115200);  //Initialize serial
  WiFi.mode(WIFI_STA);   
  ThingSpeak.begin(client);  // Initialize ThingSpeak
 

}

void loop() {
  g=analogRead(pin);  // Analogdan okunan veri, g değişkenine atandı
  gy=(g/4095)*16;  // g değikenine atanan veri, 16V ile orantılı hale getirildi
  Serial.println(gy);
  // Okunan veriler aşağıdaki If komutları içerisinde kıyaslanıp, sağlandığı koşula göre Kontrol paneline bataryanın doluluk oranı yansıtılmıştır
  if (gy>=12){ 
    delay(1000);
    writedata = 100;
  }
  else if (10<=gy && gy<12){
    delay(1000);
    writedata = 75;
  }
  else if (8<=gy && gy<10){
    delay(1000);
    writedata = 50;
  }
  else if (6<=gy && gy<8){
    delay(1000);
    writedata = 25;
  }
  else if (gy<6){
    delay(500);
    writedata = 0;}
    if ((millis() - sonyazma) > gecikme) {
      if(WiFi.status() != WL_CONNECTED){
        Serial.print("Bağlantı başarılı");
        while(WiFi.status() != WL_CONNECTED){
          WiFi.begin(id,sifre); 
          delay(5000);     
        } 
        Serial.println("\nConnected.");
      }
      int x = ThingSpeak.writeField(myChannelNumber, 1, writedata, myWriteAPIKey);
      Serial.println(writedata);
      sonyazma = millis();
    }
}
```



