
# SERVIDOR NODE

## Main
Este código configura um servidor web no ESP8266 que permite ligar e desligar um LED remotamente. Ele cria uma página HTML para o controle e utiliza uma conexão Wi-Fi.

Principais Funções:

Configuração de Rede:

Conecta o ESP8266 à rede Wi-Fi usando o SSID e senha definidos.
Configura um IP estático (local_IP, gateway e subnet) na rede.
Servidor Web:

O servidor é inicializado na porta 80 e oferece três endpoints:
/ - Página principal, onde são exibidos os botões para controlar o LED.
/5/on - Endpoint para ligar o LED.
/5/off - Endpoint para desligar o LED.
Interface HTML:

A página HTML mostra dois botões (ON e OFF) que, ao serem pressionados, acionam uma requisição para ligar ou desligar o LED.
Controle do LED:

O LED está conectado ao pino D1 (GPIO5) do ESP8266.
handleOn() liga o LED, e handleOff() desliga o LED.

## Platformio

Este código é um arquivo de configuração do PlatformIO para o ESP8266, utilizando o framework Arduino.

Configurações Especificadas:

platform: Define a plataforma ESP8266.
board: Especifica o modelo da placa como nodemcuv2.
framework: Define o framework como Arduino.
Esta configuração permite que o código seja compilado e carregado para uma placa NodeMCU ESP8266 usando o PlatformIO.








