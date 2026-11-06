# Sistema Automatizado: Porta Automática

**Unidade Curricular:** Sistemas Automatizados (USJT)
**Semana:** 1 - Fundamentos dos sistemas automatizados
**Professor:** Prof. Calvetti

## 📌 Objetivo do Sistema
Controlar o acesso a um edifício de forma segura e eficiente. O sistema deve abrir a porta automaticamente quando uma presença é detectada, mantê-la aberta durante a passagem e fechá-la após a saída do usuário, garantindo a proteção contra esmagamentos ou impactos (estado seguro).

## ⚙️ Classificação da Automação
* **Classificação:** Sistema Automático (Malha Fechada).
* **Justificativa:** O sistema percebe as condições do ambiente (presença de pessoas, obstáculos), decide e atua (abre, pausa ou fecha o motor da porta) segundo uma lógica previamente definida, sem a necessidade de intervenção humana direta para realizar o seu ciclo normal de operação. Além disso, utiliza realimentação (sensores de estado e barreira) para corrigir ou interromper a ação em andamento.

## ⚠️ Análise de Falhas e Estado Seguro
Um sistema automatizado precisa ter seu comportamento definido diante de falhas e perturbações, priorizando sempre a proteção das pessoas.

* **Perturbação Comum:** Obstáculo no caminho ou pessoa parada sob a porta.
  * **Resposta:** A barreira de segurança (realimentação) detecta a interrupção, informando a lógica que deve **parar o motor e reabrir a porta** imediatamente.
* **Falha Crítica:** Sensor de presença travado ou enviando sinal inválido.
  * **Efeito:** Risco da porta fechar indevidamente sobre alguém ou operar de forma intermitente.
  * **Comportamento Seguro (Falha Segura):** A lógica bloqueia a atuação do motor, **impedindo o fechamento automático** (mantendo a porta aberta ou travada em posição segura) e pode emitir um sinal de manutenção.

## 🧪 Casos de Teste e Validação

Abaixo estão os cenários de teste utilizados para validar a lógica do modelo funcional criado no diagrams.net:

| Cenário | Entrada | Resposta Esperada (Saída/Ação) |
| :--- | :--- | :--- |
| **1. Aproximação válida** | Presença detectada | Abrir a porta |
| **2. Passagem concluída** | Área livre | Fechar após tempo de espera |
| **3. Obstáculo** | Barreira de segurança interrompida | Parar o fechamento e reabrir |
| **4. Falha de sensor** | Sinal inválido ou travado | Impedir fechamento automático (Falha Segura) |
| **5. Emergência** | Comando prioritário (ex: Alarme de incêndio) | Adotar estado seguro (Porta totalmente aberta e travada) |

## 📁 Arquivos do Repositório
* `diagrama_porta_automatica.png`: Imagem exportada do modelo funcional.
* `diagrama_porta_automatica.drawio`: Arquivo editável gerado no diagrams.net.