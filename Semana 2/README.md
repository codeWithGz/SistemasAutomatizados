# Estação de Monitoramento de Processos

## Objetivo
Construir um protótipo funcional simulado para aquisição de sinais digitais e analógicos, classificação de grandezas e sinalização de estados[cite: 70]. 

## Equivalência Industrial
Os componentes didáticos utilizados representam instrumentos industriais correspondentes[cite: 71, 82]:
* **Pushbutton**: Fim de curso / pressostato (Sensor digital)[cite: 71].
* **Potenciômetro**: Transmissor analógico (Entrada analógica variável)[cite: 71].
* **DHT22**: RTD ou termopar integrado a um transmissor (Medição de temperatura)[cite: 71].
* **LED**: Sinaleiro ou relé atuador[cite: 71].
* **Arduino Uno**: Controlador lógico programável (CLP) ou controlador embarcado[cite: 71].

## Mapa de Pinos e Circuito
* **Botão (BTN)**: Pino digital 2 conectado com o GND interno (configurado em INPUT_PULLUP)[cite: 3, 72].
* **Sinalizador (LED)**: Pino digital 8 em série com resistor de 220 Ω ligado ao GND[cite: 3, 72].
* **Potenciômetro (POT)**: Pino analógico A0, com extremos em 5V e GND[cite: 3, 75].
* **Temperatura (DHT22)**: Pino digital 4, com alimentação em 5V e GND[cite: 3, 77].

## Regras de Estados e Prioridade
O sistema avalia as variáveis em ordem de prioridade de risco[cite: 12, 80]:
1. **FALHA DE SENSOR**: Leitura inválida (NaN) no sensor DHT22[cite: 12].
2. **ALARME**: Temperatura $\ge 40^{\circ}C$ ou potenciômetro $\ge 750$[cite: 12, 79].
3. **ATENÇÃO**: Temperatura entre $30^{\circ}C$ e $39,9^{\circ}C$ ou potenciômetro entre $400$ e $749$[cite: 12, 79].
4. **NORMAL**: Temperatura $< 30^{\circ}C$ e potenciômetro entre $0$ e $399$[cite: 12, 79].

## Limitações da Simulação Eletrônica
A simulação comprova a coerência funcional da lógica (aquisição, prioridade e atuação), mas não valida fisicamente requisitos industriais como alcance real, imunidade a ruído elétrico, grau de proteção do invólucro, calibração metrológica, compatibilidade eletromagnética e segurança funcional[cite: 14, 59, 82].
