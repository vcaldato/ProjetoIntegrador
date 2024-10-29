
## SERVIDOR NODE
Este código configura uma conexão Wi-Fi e permite controlar um LED remotamente via Telnet usando um módulo ESP8266. Além disso, ele permite atualizações Over-the-Air (OTA) para facilitar o desenvolvimento.

Resumo das Funcionalidades
Conexão Wi-Fi:

O dispositivo ESP8266 conecta-se a uma rede Wi-Fi usando o SSID e senha fornecidos.
Configuração OTA:

Através da biblioteca ArduinoOTA, o código permite atualizações OTA, com mensagens de progresso e tratamento de erros exibidas no monitor serial.
Servidor Telnet:

Um servidor Telnet é iniciado na porta 23, permitindo a conexão de um cliente.
Uma vez conectado, o cliente pode enviar os comandos:
'1': Liga o LED.
'0': Desliga o LED.
Mensagens de confirmação e de erro são enviadas ao cliente Telnet.
Controle do LED:

O LED conectado ao pino D1 do ESP8266 é ligado e desligado com os comandos recebidos via Telnet.
