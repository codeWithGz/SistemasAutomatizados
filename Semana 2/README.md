# Estação de Monitoramento de Processos

## Objetivo
Construir um protótipo funcional simulado para aquisição de sinais digitais e analógicos, classificação de grandezas e sinalização de estados. 

## Equivalência Industrial
Os componentes didáticos utilizados representam instrumentos industriais correspondentes:
* **Pushbutton**: Fim de curso / pressostato (Sensor digital).
* **Potenciômetro**: Transmissor analógico (Entrada analógica variável).
* **DHT22**: RTD ou termopar integrado a um transmissor (Medição de temperatura).
* **LED**: Sinaleiro ou relé atuador.
* **Arduino Uno**: Controlador lógico programável (CLP) ou controlador embarcado.

## Mapa de Pinos e Circuito
* **Botão (BTN)**: Pino digital 2 conectado com o GND interno (configurado em INPUT_PULLUP).
* **Sinalizador (LED)**: Pino digital 8 em série com resistor de 220 Ω ligado ao GND.
* **Potenciômetro (POT)**: Pino analógico A0, com extremos em 5V e GND.
* **Temperatura (DHT22)**: Pino digital 4, com alimentação em 5V e GND.

## Regras de Estados e Prioridade
O sistema avalia as variáveis em ordem de prioridade de risco:
1. **FALHA DE SENSOR**: Leitura inválida (NaN) no sensor DHT22.
2. **ALARME**: Temperatura $\ge 40^{\circ}C$ ou potenciômetro $\ge 750$.
3. **ATENÇÃO**: Temperatura entre $30^{\circ}C$ e $39,9^{\circ}C$ ou potenciômetro entre $400$ e $749$.
4. **NORMAL**: Temperatura $< 30^{\circ}C$ e potenciômetro entre $0$ e $399$.

## Limitações da Simulação Eletrônica
A simulação comprova a coerência funcional da lógica (aquisição, prioridade e atuação), mas não valida fisicamente requisitos industriais como alcance real, imunidade a ruído elétrico, grau de proteção do invólucro, calibração metrológica, compatibilidade eletromagnética e segurança funcional.
