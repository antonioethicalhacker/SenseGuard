/* Circuito SenseGuard
Entradas: 6 PIRs (sensores de movimento)
Saídas: 6 LEDs 
--------------------- // ---------------------
Autor: Antônio de Souza Cerdeira, Anna Júlia Xavier Martins, Gabriel Ferreira de Souza, Giovanna Calderon Lachi,
Lucca Sandri Tonel, Tchinndjia Rufina Ferreira de Gonzaga, Vinícius Estevem de Paula
--------------------- // ---------------------
Data: 20/11/2025
--------------------- // ---------------------
Versão 5.0: Versão funcional com 1 ESP32, que é responsável por realizar o acionamento de cada LED de acordo com cada PIR,
além de possuir um monitoramento por Bluetooth BLE pelo aplicativo LightBlue.
--------------------- // ---------------------
Version 5.0: Functional version with 1 ESP32, responsible for controlling each LED according to each PIR, 
and also providing monitoring via BLE Bluetooth through the LightBlue app.
*/
#include <BLEDevice.h>
#include <BLEServer.h>
#include <BLEUtils.h>
#include <BLE2902.h>

// ----- PINAGEM -----
int pirPins[6] = {36, 39, 34, 35, 32, 33};   // Entrada PIR
int ledPins[6] = {2, 4, 21, 19, 12, 18};      // LEDs
int pirState[6] = {0, 0, 0, 0, 0, 0};

// Nomes dos ambientes
String nomesAmbientes[6] = {
  "Quarto",
  "Banheiro",
  "Corredor1",
  "Corredor2",
  "Banheiro1",
  "Banheiro2"
};

// BLE
BLECharacteristic *estadoChar;

void setup() {
  Serial.begin(9600);
  delay(500);

  Serial.println("Iniciando sistema BLE + PIR + LED...");
  Serial.println("Aguarde 25~30 segundos para estabilizar os PIRs!");

  // Configura PIR e LED
  for (int i = 0; i < 6; i++) {
    pinMode(pirPins[i], INPUT);
    pinMode(ledPins[i], OUTPUT);
    digitalWrite(ledPins[i], LOW);
  }

  // ------ CONFIGURAÇÃO BLE -------
  BLEDevice::init("CASA-ESP32");
  BLEServer *server = BLEDevice::createServer();

  BLEService *service = server->createService("1234");

  estadoChar = service->createCharacteristic(
      "ABCD",
      BLECharacteristic::PROPERTY_READ |
      BLECharacteristic::PROPERTY_NOTIFY
  );

  estadoChar->addDescriptor(new BLE2902());
  estadoChar->setValue("INICIADO");

  service->start();

  // Configura anúncios BLE
  BLEAdvertising *advertising = BLEDevice::getAdvertising();
  advertising->addServiceUUID("1234");   // garante que apps detectem o service
  advertising->setScanResponse(true);
  advertising->start();

  Serial.println("BLE ativo! Abra o LightBlue ou nRF Connect e conecte.");
}

void enviaMensagem(String msg) {
  Serial.println(msg);
  estadoChar->setValue(msg.c_str());  // garante string UTF-8 simples
  estadoChar->notify();
  delay(20);
}

void loop() {
  for (int i = 0; i < 6; i++) {
    int leitura = digitalRead(pirPins[i]);

    if (leitura != pirState[i]) {
      pirState[i] = leitura;

      if (leitura == HIGH) {
        digitalWrite(ledPins[i], HIGH);
        delay (5000);
        enviaMensagem(nomesAmbientes[i] + "_ON");   // Nome do ambiente + ON
      } else {
        digitalWrite(ledPins[i], LOW);
        enviaMensagem(nomesAmbientes[i] + "_OFF");  // Nome do ambiente + OFF
      }
    }
  }

  delay(50);
}