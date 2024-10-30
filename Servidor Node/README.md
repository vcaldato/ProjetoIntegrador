# Servidor Node

Este projeto configura o ESP8266 como um servidor web, permitindo o controle de um LED através de uma interface web. Ao acessar o servidor, você pode ligar e desligar o LED remotamente.

## Sumário

- [Componentes Necessários](#componentes-necessários)
- [Código Completo](#código-completo)
- [Explicação do Código](#explicação-do-código)
  - [Bibliotecas Necessárias](#bibliotecas-necessárias)
  - [Configuração Wi-Fi](#configuração-wi-fi)
  - [Função setup()](#função-setup)
  - [Função loop()](#função-loop)
  - [Manipuladores de Rota](#manipuladores-de-rota)

---

## Componentes Necessários

- **ESP8266**
- **Cabo USB** para conexão ao computador
- **LED** e **resistor** para controle do LED (se necessário)

---

## Código Completo

```cpp
#include <ESP8266WiFi.h>
#include <ESP8266WebServer.h>

const char* ssid     = "Prof.Rafael";
const char* password = "senhasenha";

ESP8266WebServer server(80);

const int output5 = 5; // D1

// Você precisa colocar o IP da sua faixa
IPAddress local_IP(192, 168, 125, 156); // Troque o IP
IPAddress gateway(192, 168, 125, 1);
IPAddress subnet(255, 255, 255, 0);

void handleRoot();
void handleOn();
void handleOff();

void handleRoot() {
  String html = "<!DOCTYPE html><html>";
  html += "<head><meta name=\"viewport\" content=\"width=device-width, initial-scale=1\">";
  html += "<link rel=\"icon\" href=\"data:,\">";
  html += "<style>html { font-family: Helvetica; display: inline-block; margin: 0px auto; text-align: center;}";
  html += ".button { background-color: #195B6A; border: none; color: white; padding: 16px 40px;";
  html += "text-decoration: none; font-size: 30px; margin: 2px; cursor: pointer;}";
  html += ".button2 {background-color: #77878A;}</style></head>";
  html += "<body><h1>ESP8266 Web Server</h1>";
  html += "<h2>Prof. Rafael</h2>";
  html += "<button onclick=\"toggleLED('on')\" class=\"button\">ON</button>";
  html += "<button onclick=\"toggleLED('off')\" class=\"button button2\">OFF</button>";
  html += "<script>";
  html += "function toggleLED(state) {";
  html += "  var xhr = new XMLHttpRequest();";
  html += "  xhr.open('GET', '/5/' + state, true);";
  html += "  xhr.send();";
  html += "}";
  html += "</script>";
  html += "</body></html>";
  server.send(200, "text/html", html);
}

void handleOn() {
  digitalWrite(output5, HIGH);
  server.send(200, "text/plain", "LED Ligado!");
}

void handleOff() {
  digitalWrite(output5, LOW);
  server.send(200, "text/plain", "LED Desligado!");
}

void setup() {
  Serial.begin(9600);
  pinMode(output5, OUTPUT);
  digitalWrite(output5, LOW);
 
  if (!WiFi.config(local_IP, gateway, subnet)) {
    Serial.println("STA Failed to configure");
  }
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  Serial.println("");
  Serial.println("WiFi connected.");
  Serial.println("IP address: ");
  Serial.println(WiFi.localIP());
 
  server.on("/", HTTP_GET, handleRoot);
  server.on("/5/on", HTTP_GET, handleOn);
  server.on("/5/off", HTTP_GET, handleOff);
  server.begin();
}

void loop() {
  server.handleClient();
}
```
# Configuração Wi-Fi
```cpp
const char* ssid = "Prof.Rafael";
const char* password = "senhasenha";
```
# Função setup()
A função setup() é responsável pela inicialização do sistema, incluindo a configuração do pino de saída e a conexão à rede Wi-Fi.]
```cpp
void setup() {
  Serial.begin(9600);
  pinMode(output5, OUTPUT);
  digitalWrite(output5, LOW);
```
# Configuração de IP
Define o IP local do dispositivo, o gateway e a máscara de sub-rede. Você deve ajustar o IP conforme sua rede.
```cpp
IPAddress local_IP(192, 168, 125, 156);
IPAddress gateway(192, 168, 125, 1);
IPAddress subnet(255, 255, 255, 0);
```
# Verificação da Conexão Wi-Fi
A função loop() mantém o servidor web ativo, permitindo que ele processe as requisições dos clientes.
```cpp
void loop() {
  server.handleClient();
}
```
# Manipuladores de Rota
Os manipuladores de rota (handleRoot, handleOn, handleOff) definem as respostas do servidor web a diferentes URLs.
## Manipulador da Página Principal
```cpp
void handleRoot() {
  // Gera a página HTML
}
```
## Manipulador para Ligar o LED
```cpp
void handleOn() {
  digitalWrite(output5, HIGH);
  server.send(200, "text/plain", "LED Ligado!");
}
```
## Manipulador para Desligar o LED
```cpp
void handleOff() {
  digitalWrite(output5, LOW);
  server.send(200, "text/plain", "LED Desligado!");
}
```
