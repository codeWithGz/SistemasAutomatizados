# UC Sistemas Automatizados — Semana 10: Modbus, OPC UA e MQTT
---

## 1. Artefatos Entregues

* **Mapa Modbus e Transações (`.xlsx`):** [`./modbus/mapa_modbus.xlsx`](./modbus/mapa_modbus.xlsx)
* **Dicionário Semântico OPC UA (`.csv`):** [`./opcua/tags_opcua.csv`](./opcua/tags_opcua.csv)
* **Diagrama Editável da Arquitetura Integrada e MQTT (`.drawio`):** [`./mqtt/mqtt.drawio`](./mqtt/mqtt.drawio)
* **Simulador de Roteamento e Exercícios Adicionais 1–5 (`.xlsx`):** [`./mqtt/roteamento.xlsx`](./mqtt/roteamento.xlsx)
* **Casos de Teste Documentais (`.csv`):** [`./testes/casos_teste.csv`](./testes/casos_teste.csv)
* **Versão de Leitura dos Diagramas (`.pdf`):** [`./evidencias/diagramas.pdf`](./evidencias/diagramas.pdf)

---

## 2. Escopo e Arquitetura Combinada (Tanque T-101)

A solução integra o **Tanque T-101** (medição de nível `LT-101`, temperatura `TT-101`, bomba `P-101` e setpoint `SP_NIVEL`) em **três zonas segmentadas**:

1. **Zona de Controle:** `CLP T-101`, E/S remota e servidor legado **Modbus** expondo bobinas e registradores. Toda a lógica determinística e os intertravamentos de segurança permanecem exclusivamente no CLP.
2. **Zona de Integração:** **Gateway / Servidor OPC UA** que lê o mapa Modbus com função mínima, normaliza os dados em nós semânticos (`NodeId`, tipo, unidade, `StatusCode`, `SourceTimestamp` e permissões `AccessLevel`/`UserAccessLevel`) e alimenta o **Publisher MQTT (`pub-t101`)**.
3. **Zona de Serviços:** **Broker MQTT** com TLS, autenticação e ACL por tópico, distribuindo payloads selecionados para **HMI (`hmi-linha1`)**, **Histórico (`historiador`)**, **Alarmes (`alarme-t101`)** e **Manutenção (`manutencao`)**.

---

## 3. Mapa Modbus Resolvido (`T-101`)

| Referência | Offset | Área | Função (L/E) | Tipo | Comprimento | Escala / Ordem | Unidade | Acesso | Variável |
| :---: | :---: | :--- | :---: | :---: | :---: | :--- | :---: | :---: | :--- |
| `00001` | `0` | Coil | `01` / `05` | `BOOL` | 1 bit | 1:1 | — | RW | `P101_CMD` |
| `10001` | `0` | Discrete Input | `02` / — | `BOOL` | 1 bit | 1:1 | — | R | `P101_RUN` |
| `30001` | `0` | Input Register | `04` / — | `UINT16` | 1 reg | 1:1 (0–65535) | `count` | R | `LT101_RAW` |
| `40001` | `0` | Holding Register | `03` / `06` | `UINT16` | 1 reg | Escala `0,1%` | `%` | RW | `SP_NIVEL` |
| `40002-40003` | `1-2` | Holding Register | `03` / — | `FLOAT32` | 2 regs | Big-Endian (`ABCD`) | `°C` | R | `TEMP_REAL` |

* **Leitura conjunta (`SP_NIVEL` + `TEMP_REAL`):** Função **`03` (Read Holding Registers)**, **offset inicial `0`**, **quantidade `3` registradores** (offsets `0`, `1` e `2`), aplicando escala `0,1%` em `40001` e ordem de palavras validada em `40002-40003`.
* **Teste de endereço inválido:** Solicitar offset fora do mapa (`40099` / offset `98`) deve retornar exceção **`02 — Illegal Data Address`**, nunca um valor `0` tratado como leitura válida.

---

## 4. Dicionário OPC UA (`tags_opcua.csv`)

| NodeId | BrowseName | DataType | Unidade | AccessLevel | Origem Modbus | Qualidade & Tempo |
| :--- | :--- | :---: | :---: | :---: | :---: | :--- |
| `ns=2;s=P101.Cmd` | `StartCommand` | `Boolean` | — | `CurrentReadWrite` | `00001/FC05` | `StatusCode` + `SourceTimestamp` |
| `ns=2;s=P101.Run` | `Running` | `Boolean` | — | `CurrentRead` | `10001/FC02` | `StatusCode` + `SourceTimestamp` |
| `ns=2;s=T101.LevelRaw` | `LevelRaw` | `UInt16` | `count` | `CurrentRead` | `30001/FC04` | `StatusCode` + `SourceTimestamp` |
| `ns=2;s=T101.LevelSP` | `LevelSetpoint` | `Double` | `%` | `CurrentReadWrite` | `40001/FC03/06` | `StatusCode` + `SourceTimestamp` |
| `ns=2;s=T101.Temp` | `Temperature` | `Double` | `°C` | `CurrentRead` | `40002-3/FC03` | `StatusCode` + `SourceTimestamp` |

---

## 5. Contrato MQTT (Tópicos, Payload JSON, QoS, RETAIN e ACL)

* **Tópicos oficiais:** `fabrica/linha1/tanque101/temperatura`, `.../nivel`, `.../estado` e `.../alarme`.
* **QoS e RETAIN:** `QoS 1` para indicadores e alarmes (consumidores tratam duplicidade via timestamp); `RETAIN = true` apenas para estado atual, e `RETAIN = false` para eventos transitórios.
* **ACLs mínimas:** `pub-t101` (`PUBLISH fabrica/linha1/tanque101/#`), `hmi-linha1` (`SUBSCRIBE fabrica/linha1/+/+`), `historiador` (`SUBSCRIBE fabrica/+/+/temperatura`), `alarme-t101` (`SUBSCRIBE fabrica/linha1/tanque101/#`).

```json
{
  "schemaVersion": 1,
  "source": "T101",
  "ts": "2026-11-10T20:00:00Z",
  "value": 78.4,
  "unit": "degC",
  "quality": "GOOD",
  "alarm": "HIGH"
}
```

---

## 6. Síntese dos 5 Exercícios Adicionais (Detalhados em `mqtt/roteamento.xlsx`)

1. **Curingas (`Ext1_Curingas`):** 10 publicações avaliadas frente a 3 filtros (`fabrica/+/temperatura`, `fabrica/linha1/#` e `fabrica/linha1/tanque101/temperatura`), cobrindo ausência de nível, nível extra, mudança de raiz (`plantaB`) e mudança de último nível (`estado`).
2. **QoS e Retenção (`Ext2_QoS_Retain`):** Matriz de 5 cenários distinguindo telemetria (`QoS 0`, `RETAIN=NAO`), estado de bomba (`QoS 1`, `RETAIN=SIM`), alarme com deduplicação (`QoS 1`, `RETAIN=NAO`), pulso transitório (`QoS 1`, `RETAIN=NAO`) e operação crítica de duplicidade (`avaliar QoS 2`, `RETAIN=NAO`).
3. **Segurança entre Zonas (`Ext3_Seguranca`):** Matriz de fluxos explícitos com firewall, certificados X.509/trust list no OPC UA, TLS + ACL no MQTT e **bloqueio explícito (default deny)** de qualquer rota direta do Broker MQTT para o CLP.
4. **Comparação por Requisito (`Ext4_Comparacao`):** Recomendação justificada entre Modbus (legado), OPC UA (semântica e descoberta), MQTT (distribuição 1-para-N) e controle local dedicado (segurança determinística).
5. **Falha e Recuperação (`Ext5_Falhas`):** Efeito esperado, detecção e retorno controlado para indisponibilidade do servidor Modbus, reinício do gateway, queda do broker, certificado expirado e payload incompatível.

---

## 7. Casos de Teste (`T01` a `T06`)

| ID | Cenário | Resultado Esperado | Obtido Documental | Conclusão |
| :---: | :--- | :--- | :--- | :---: |
| **`T01`** | Ler `40001` com `FC03` | `SP_NIVEL` convertido (`0,1%`) | Offset `0` e escala documentados | Aprovado |
| **`T02`** | Ler offset inexistente | Exceção `02` (`Illegal Data Address`) | Caso e exceção registrados | Aprovado |
| **`T03`** | Qualidade OPC UA `Bad` | Consumidor rejeita valor inválido | Regra de propagação documentada | Aprovado |
| **`T04`** | Filtro `fabrica/linha1/+/temperatura` | Somente temperaturas da linha 1 | Casos positivos/negativos coincidem | Aprovado |
| **`T05`** | Novo assinante com `RETAIN` | Recebe último estado retido + `ts` | Contrato separa estado e evento | Aprovado |
| **`T06`** | Cliente sem permissão | Operação recusada pelo broker | ACL impede ação fora do escopo | Aprovado |

---

## 8. Limites da Simulação e Política de Segredos

Os artefatos documentam o mapeamento de memória, a modelagem semântica, o contrato de roteamento e as fronteiras de segurança, mas **não estabelecem comunicação real nem comprovam latência, interoperabilidade em rede ou configuração efetiva de certificados/firewalls**. Nenhuma credencial, senha ou chave privada real foi incluída neste repositório.
