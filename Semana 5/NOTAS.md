# Notas Técnicas, Limites e Melhorias — TP05 (Semáforo Temporizado)

## 1. Limites da Especificação Funcional (GRAFCET vs. Implementação Física)
* **Natureza puramente temporal (malha aberta):** O modelo da Atividade 24 evolui exclusivamente por temporização interna associada ao tempo de ativação de cada etapa (`X0`, `X1`, `X2`), sem realimentação de sensores de fluxo veicular, botoeira de pedestres ou monitoramento de corrente das lâmpadas.
* **Diferença entre GRAFCET (IEC 60848) e CLP (IEC 61131-3):** O diagrama GRAFCET descreve o comportamento sequencial ideal e assume evolução instantânea entre etapas. Na implementação em CLP (linguagem SFC ou Ladder), existe o tempo de varredura (*scan time*), que introduz um atraso determinístico de um ciclo de leitura/escrita nas transições temporais.
* **Ausência de modo de falha / manutenção:** O ciclo normal não contempla um modo degradado (como amarelo intermitente em caso de falha de controlador ou horário de baixo fluxo) nem intertravamento físico para um cruzamento de duas vias.

## 2. Melhorias Recomendadas para Aplicação Real
1. **Intertravamento de Hardware (Fail-Safe):** Em cruzamentos reais com dois semáforos ortogonais, deve-se utilizar relés de segurança ou contatos auxiliares fisicamente intertravados para impedir a energização simultânea de dois sinais verdes (`VERDE_VIA_A AND VERDE_VIA_B = 0`), mesmo diante de falha de software.
2. **Diagnóstico de Queima de Lâmpada (Watchdog de Corrente):** Monitorar a corrente do circuito vermelho (`VERMELHO = 1`); caso a lâmpada vermelha rompa, o controlador deve comutar imediatamente para modo de alerta (amarelo intermitente) ou sinalizar falha à central.
3. **Tempo de Segurança (Vermelho Total / *All-Red*):** Em cruzamentos com múltiplas fases, inserir uma etapa intermediária de $1\text{ s}$ a $2\text{ s}$ com todos os sinais em vermelho após o amarelo, garantindo a evacuação completa do cruzamento antes de liberar a via transversal.
