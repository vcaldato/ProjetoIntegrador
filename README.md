# ProjetoIntegrador

## OTA
Este projeto permite conectar um ESP8266 a uma rede Wi-Fi e realizar atualizações Over-the-Air (OTA) usando a biblioteca ArduinoOTA.

Passos a passos:
Conecta o ESP8266 ao Wi-Fi e exibe o IP no monitor serial.
Configura eventos OTA:
onStart: Início da atualização.
onEnd: Fim da atualização.
onProgress: Progresso da atualização.
onError: Exibe erros, caso ocorram.
No loop principal, ArduinoOTA.handle() mantém o dispositivo pronto para receber atualizações OTA.

Uso:
Conecta o ESP8266 à rede.
Seleciona o IP exibido no monitor serial na aba Portas de Rede na Arduino IDE.
Faz o upload OTA diretamente pela rede.
