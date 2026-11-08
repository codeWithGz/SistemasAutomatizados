# Casos de Teste Integrados

| Caso | Potenciômetro | Temperatura | Botão | Resposta Esperada | Justificativa |
|---|---|---|---|---|---|
| **T1** | 200 | $25^{\circ}C$ | Livre | NORMAL; LED off | Todas as entradas encontram-se na faixa normal[cite: 12]. |
| **T2** | 500 | $25^{\circ}C$ | Livre | ATENÇÃO; LED off | O valor do potenciômetro está na faixa entre 400 e 749[cite: 12]. |
| **T3** | 800 | $25^{\circ}C$ | Livre | ALARME; LED off | O valor do potenciômetro é $\ge 750$[cite: 12]. |
| **T4** | 200 | $35^{\circ}C$ | Livre | ATENÇÃO; LED off | A temperatura encontra-se entre 30 e $<40^{\circ}C$[cite: 12]. |
| **T5** | 200 | $45^{\circ}C$ | Livre | ALARME; LED off | A temperatura é $\ge 40^{\circ}C$[cite: 12]. |
| **T6** | 200 | $25^{\circ}C$ | Pressionado | NORMAL; LED on | O botão atua no LED sem interferir no estado analógico[cite: 12]. |
| **T7** | 200 | Inválida | Livre | FALHA DE SENSOR | O diagnóstico de leitura corrompida (NaN) tem prioridade máxima[cite: 12]. |
