
//------------------------------------------------------------------------------
// Librerias y Variables
#include "ThingSpeak.h"
#include "WiFi.h"
#define INDICADOR 2
#define ALERTA 15
#define BOCINA 0
#define SENSOR 34
#define DELAY 500

//Variables para la configuracion de Acceso
const char* ssid = "Tenda_495AA8";
const char* password = "amarillo";

//Variables para la conexion de ThingSpeak
unsigned long channelID = 1216013;
const char* WriteAPIKey = "XHZWXAE0SHTN89VP";

WiFiClient Client;

// Variable asignada para el sensor MQ6
int sensorValue;  

//------------------------------------------------------------------------------
//Configuracion Incial
void setup() {
  Serial.begin(115200);

 //Conexion a la RED
  WiFi.begin(ssid,password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  Serial.println("Wifi Conectado");

  //Conexion a ThingSpeak
  ThingSpeak.begin(Client);

  //Configuracion de Salidas
  pinMode(INDICADOR, OUTPUT);
  pinMode(BOCINA, OUTPUT);
  pinMode(ALERTA, OUTPUT);
 
  Serial.println("Monitoreo MQ6");
}
//------------------------------------------------------------------------------
void loop() {
  digitalWrite(INDICADOR, HIGH);
  delay(DELAY);              

  //Lectura del Sensor MQ6 y Acodicionamiento de las Salidas
  sensorValue = analogRead(SENSOR);
  if(sensorValue < 2000){
    digitalWrite(BOCINA, HIGH);
    digitalWrite(ALERTA, LOW);
    }else{
      digitalWrite(BOCINA, LOW);
      digitalWrite(ALERTA, HIGH);
      }
  Serial.print("Value: "); Serial.println(sensorValue);
  digitalWrite(INDICADOR, LOW);
  
  //Envio de Datos al Servidor
  ThingSpeak.setField (1,sensorValue);
  ThingSpeak.writeFields(channelID,WriteAPIKey);
  Serial.println("Datos enviados a thing Speak");
  delay(DELAY);
}

//------------------------------------------------------------------------------
