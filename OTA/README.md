# ESP8266 com Conexão Wi-Fi e Atualização OTA

Este projeto configura o ESP8266 para conectar-se a uma rede Wi-Fi e habilita a atualização de firmware via OTA (Over-the-Air). Com isso, você pode atualizar o código remotamente sem a necessidade de um cabo físico após o upload inicial.

## Sumário

- [Componentes Necessários](#componentes-necessários)
- [Código Completo](#código-completo)
- [Explicação do Código](#explicação-do-código)
  - [Bibliotecas Necessárias](#bibliotecas-necessárias)
  - [Configuração Wi-Fi](#configuração-wi-fi)
  - [Função setup()](#função-setup)
  - [Configuração dos Eventos OTA](#configuração-dos-eventos-ota)
  - [Função loop()](#função-loop)


---

## Componentes Necessários

- **ESP8266**
- **Cabo USB** para conexão ao computador

---

## Código Completo

```cpp
#include <Arduino.h>
#include <ESP8266WiFi.h>
#include <ArduinoOTA.h>

const char* ssid = "Vinicius";     // Nome da rede Wi-Fi
const char* password = "vini1234"; // Senha da rede Wi-Fi

void setup() {
  Serial.begin(9600);
  WiFi.mode(WIFI_STA);
  WiFi.begin(ssid, password);

  while (WiFi.waitForConnectResult() != WL_CONNECTED) {
    Serial.println("Conexão falhou! Reiniciando...");
    WiFi.begin(ssid, password);
    delay(5000);
  }

  ArduinoOTA.onStart([]() {
    Serial.println("Iniciando atualização OTA");
  });

  ArduinoOTA.onEnd([]() {
    Serial.println("\nAtualização finalizada");
  });

  ArduinoOTA.onProgress([](unsigned int progress, unsigned int total) {
    Serial.printf("Progresso: %u%%\r", (progress / (total / 100)));
  });

  ArduinoOTA.onError([](ota_error_t error) {
    Serial.printf("Erro [%u]: ", error);
    if (error == OTA_AUTH_ERROR) Serial.println("Erro de autenticação");
    else if (error == OTA_BEGIN_ERROR) Serial.println("Erro ao iniciar");
    else if (error == OTA_CONNECT_ERROR) Serial.println("Erro de conexão");
    else if (error == OTA_RECEIVE_ERROR) Serial.println("Erro ao receber dados");
  });

  ArduinoOTA.begin();
  Serial.println("Pronto para OTA");
  Serial.print("IP: ");
  Serial.println(WiFi.localIP());
}

void loop() {
  ArduinoOTA.handle();
}
```
# Configuração Wi-Fi
```cpp
const char* ssid = "Vinicius";
const char* password = "vini1234";
```

# Função setup()
```cpp
void setup() {
  Serial.begin(9600);  // Inicia a comunicação Serial para monitorar o processo
  WiFi.mode(WIFI_STA); // Define o ESP para o modo "Station"
  WiFi.begin(ssid, password); // Conecta usando as credenciais Wi-Fi

```
# Verificação da Conexão: 
Se a conexão falhar, o ESP tenta reconectar a cada 5 segundos até que a conexão seja bem-sucedida.
```cpp
 while (WiFi.waitForConnectResult() != WL_CONNECTED) {
    Serial.println("Conexão falhou! Reiniciando...");
    WiFi.begin(ssid, password);
    delay(5000); // Tenta reconectar a cada 5 segundos
  }
```
# Configuração dos Eventos OTA
Configura os eventos que monitoram e exibem o progresso e os erros da atualização OTA.

  ```cpp
 ArduinoOTA.onStart([]() { Serial.println("Iniciando atualização OTA"); });
  ArduinoOTA.onEnd([]() { Serial.println("\nAtualização finalizada"); });
  ArduinoOTA.onProgress([](unsigned int progress, unsigned int total) {
    Serial.printf("Progresso: %u%%\r", (progress / (total / 100)));
  });
  ArduinoOTA.onError([](ota_error_t error) {
    Serial.printf("Erro [%u]: ", error);
    if (error == OTA_AUTH_ERROR) Serial.println("Erro de autenticação");
    else if (error == OTA_BEGIN_ERROR) Serial.println("Erro ao iniciar");
    else if (error == OTA_CONNECT_ERROR) Serial.println("Erro de conexão");
    else if (error == OTA_RECEIVE_ERROR) Serial.println("Erro ao receber dados");
  });
```
# Inicialização do OTA e Exibição do IP
```cpp
  ArduinoOTA.begin(); // Inicia o serviço OTA
  Serial.println("Pronto para OTA");
  Serial.print("IP: ");
  Serial.println(WiFi.localIP());  // Exibe o IP do dispositivo
}
```
ArduinoOTA.begin(): Inicia o serviço OTA.
WiFi.localIP(): Exibe o IP do dispositivo para usá-lo nas atualizações OTA.

# Função loop()
ArduinoOTA.handle(): Mantém o ESP8266 pronto para receber atualizações OTA.
```cpp
void loop() {
  ArduinoOTA.handle(); // Verifica se há uma nova atualização OTA
}
```

