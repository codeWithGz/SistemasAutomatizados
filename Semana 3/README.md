# Semana 3 - Sinais analógicos e digitais

## Links
* **Projeto Wokwi:** [https://wokwi.com/projects/480159818060944385](https://wokwi.com/projects/480159818060944385)
* **Google Planilhas:** [https://docs.google.com/spreadsheets/d/1HzsgWqRdNV3ngHa3UoVaLWH41q7PyGi7OyUqOgNHsuM/edit?usp=sharing](https://docs.google.com/spreadsheets/d/1HzsgWqRdNV3ngHa3UoVaLWH41q7PyGi7OyUqOgNHsuM/edit?usp=sharing)

## Tabela de E/S
| Tag | Endereço | Tipo | Faixa/Estado | Função |
|---|---|---|---|---|
| BTN_COMANDO | D2 | DI | HIGH/LOW | Reconhecer comando |
| POT_BRUTO | A0 | AI | 0-1023 | Adquirir variável |
| LED_ALARME | D8 | DO | OFF/ON | Sinalizar perigo |
| NIVEL_PCT | Memória | REAL | 0-100% | Decidir e registrar |

## Casos de Teste
| Caso | Entrada Simulação | Esperado | Obtido | Situação |
|---|---|---|---|---|
| A1 | Botão solto; pot mínimo | 0; valor próximo de 0 | 0; 0 | Aprovado |
| A4 | Botão solto; pot máximo | 0; valor próximo de 1023 | 0; 1023 | Aprovado |
| B1 | Percentual em 70% | ATENÇÃO | ATENÇÃO | Aprovado |
| B2 | Percentual em 85% | PERIGO | PERIGO | Aprovado |
| B3 | Percentual em -1% / 100,01%| FALHA (Fora de faixa) | FALHA | Aprovado |

## Conclusão e Limitações
A simulação comprova a funcionalidade da lógica de estados e os cálculos matemáticos de conversão analógico-digital. No entanto, ferramentas como Wokwi e planilhas servem apenas para teste de software; elas não validam precisão metrológica, ruídos eletromagnéticos reais, características de aterramento ou falhas de alimentação (como zero vivo) características de um transmissor físico industrial.
