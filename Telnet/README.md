# ESP8266 com Controle de LED via Telnet e Atualização OTA

Este projeto configura o ESP8266 para se conectar a uma rede Wi-Fi, permite o controle de um LED através de uma conexão Telnet e habilita a atualização de firmware via OTA (Over-the-Air).

## Sumário

- [Componentes Necessários](#componentes-necessários)
- [Código Completo](#código-completo)
- [Explicação do Código](#explicação-do-código)
  - [Bibliotecas Necessárias](#bibliotecas-necessárias)
  - [Configuração Inicial](#configuração-inicial)
  - [Função setup()](#função-setup)
  - [Função loop()](#função-loop)

---

## Componentes Necessários

- **ESP8266**
- **Cabo USB** para conexão ao computador
- **LED** e um resistor para o controle do LED

---

## Código Completo

```cpp
#include <ESP8266WiFi.h>
#include <ArduinoOTA.h>

const char* ssid = "ssid";          // Nome da rede Wi-Fi
const char* password = "password";  // Senha da rede Wi-Fi

const int LED_PIN = 5; // D1

WiFiServer server(23);
WiFiClient client;

void setup() {
  pinMode(LED_PIN, OUTPUT);
  Serial.begin(9600);
  WiFi.mode(WIFI_STA);
  WiFi.begin(ssid, password);

  while (WiFi.waitForConnectResult() != WL_CONNECTED) {
    Serial.println("Conexão falhou, tentando novamente...");
    WiFi.begin(ssid, password);
    delay(5000);
  }

  ArduinoOTA.onStart([]() {
    Serial.println("Iniciando atualização OTA...");
  });

  ArduinoOTA.onEnd([]() {
    Serial.println("\nAtualização concluída.");
  });

  ArduinoOTA.onProgress([](unsigned int progress, unsigned int total) {
    Serial.printf("Progresso: %u%%\r", (progress / (total / 100)));
  });

  ArduinoOTA.onError([](ota_error_t error) {
    Serial.printf("Erro[%u]: ", error);
    if (error == OTA_AUTH_ERROR) Serial.println("Falha na autenticação");
    else if (error == OTA_BEGIN_ERROR) Serial.println("Falha no início");
    else if (error == OTA_CONNECT_ERROR) Serial.println("Falha na conexão");
    else if (error == OTA_RECEIVE_ERROR) Serial.println("Falha no recebimento");
    else if (error == OTA_END_ERROR) Serial.println("Falha na finalização");
  });

  ArduinoOTA.begin();
  Serial.println("Pronto para atualizações OTA.");
  Serial.print("Endereço IP: ");
  Serial.println(WiFi.localIP());

  server.begin();
  server.setNoDelay(true);
  Serial.println("Servidor Telnet iniciado...");
}

void loop() {
  ArduinoOTA.handle();
  if (server.hasClient()) {
    if (!client || !client.connected()) {
      if (client) client.stop();
      client = server.available();
      Serial.println("Cliente conectado via Telnet.");
      client.println("Digite '1' para ligar o LED e '0' para desligar o LED.");
    } else {
      server.available().stop();
    }
  }

  if (client && client.connected() && client.available()) {
    char command = client.read();
    if (command == '1') {
      digitalWrite(LED_PIN, LOW); 
      client.println("LED ligado.");
      Serial.println("LED ligado.");
    } else if (command == '0') {
      digitalWrite(LED_PIN, HIGH); 
      client.println("LED desligado.");
      Serial.println("LED desligado.");
    } else {
      client.println("Comando inválido. Digite '1' para ligar o LED e '0' para desligar o LED.");
    }
  }
}
```
## Configuração Inicial
Define as credenciais da rede Wi-Fi e configura o pino do LED como saída.
```cpp
const char* ssid = "ssid";          // Nome da rede Wi-Fi
const char* password = "password";  // Senha da rede Wi-Fi
const int LED_PIN = 5; // D1
```
## Função setup()
 Inicia a comunicação serial, conecta o ESP8266 à rede Wi-Fi e configura as atualizações OTA, além de preparar o servidor Telnet para conexões.
```cpp
void setup() {
  pinMode(LED_PIN, OUTPUT);  // Configura o pino do LED como saída
  Serial.begin(9600);        // Inicia a comunicação Serial para monitorar o processo
  WiFi.mode(WIFI_STA);       // Define o ESP para o modo "Station"
  WiFi.begin(ssid, password); // Conecta usando as credenciais Wi-Fi

  while (WiFi.waitForConnectResult() != WL_CONNECTED) {
    Serial.println("Conexão falhou, tentando novamente...");
    WiFi.begin(ssid, password);
    delay(5000); // Tenta reconectar a cada 5 segundos
  }
```
## Configuração dos Eventos OTA
 Estabelece eventos que monitoram e reportam o progresso, finalização e erros durante as atualizações OTA.
```cpp
ArduinoOTA.onStart([]() {
    Serial.println("Iniciando atualização OTA...");
});

ArduinoOTA.onEnd([]() {
    Serial.println("\nAtualização concluída.");
});

ArduinoOTA.onProgress([](unsigned int progress, unsigned int total) {
    Serial.printf("Progresso: %u%%\r", (progress / (total / 100)));
});

ArduinoOTA.onError([](ota_error_t error) {
    Serial.printf("Erro[%u]: ", error);
    if (error == OTA_AUTH_ERROR) Serial.println("Falha na autenticação");
    else if (error == OTA_BEGIN_ERROR) Serial.println("Falha no início");
    else if (error == OTA_CONNECT_ERROR) Serial.println("Falha na conexão");
    else if (error == OTA_RECEIVE_ERROR) Serial.println("Falha no recebimento");
    else if (error == OTA_END_ERROR) Serial.println("Falha na finalização");
});

```
## Inicialização do OTA e Servidor Telnet
Ativa o serviço OTA, exibe o endereço IP do dispositivo e inicia o servidor Telnet, permitindo conexões de clientes para controle remoto.
```cpp
ArduinoOTA.begin(); // Inicia o serviço OTA
Serial.println("Pronto para atualizações OTA.");
Serial.print("Endereço IP: ");
Serial.println(WiFi.localIP());  // Exibe o IP do dispositivo
server.begin(); // Inicia o servidor Telnet
server.setNoDelay(true); // Configura o servidor para não atrasar a resposta
Serial.println("Servidor Telnet iniciado...");

```
## Função loop()
A função loop() mantém o ESP8266 pronto para receber atualizações OTA e gerencia as conexões do cliente via Telnet.
```cpp
void loop() {
    ArduinoOTA.handle(); // Verifica se há uma nova atualização OTA
    if (server.hasClient()) {
        if (!client || !client.connected()) {
            if (client) client.stop();
            client = server.available(); // Aceita um novo cliente
            Serial.println("Cliente conectado via Telnet.");
            client.println("Digite '1' para ligar o LED e '0' para desligar o LED.");
        } else {
            server.available().stop(); // Para a conexão anterior se um novo cliente se conectar
        }
    }

    if (client && client.connected() && client.available()) {
        char command = client.read(); // Lê o comando do cliente
        if (command == '1') {
            digitalWrite(LED_PIN, LOW); 
            client.println("LED ligado.");
            Serial.println("LED ligado.");
        } else if (command == '0') {
            digitalWrite(LED_PIN, HIGH); 
            client.println("LED desligado.");
            Serial.println("LED desligado.");
        } else {
            client.println("Comando inválido. Digite '1' para ligar o LED e '0' para desligar o LED.");
        }
    }
}

```

