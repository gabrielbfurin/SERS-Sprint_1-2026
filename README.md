# ChargeGrid Intelligence
### Plataforma de Gestão Inteligente de Eletropostos Comerciais

> **EV Challenge 2026 — Sprint 1**  
> Automação, eficiência e sustentabilidade na infraestrutura de recarga de veículos elétricos.

---

## Problema + Justificativa

Eletropostos comerciais enfrentam desafios críticos na operação:

- **Falta de integração**: carregadores de diferentes fabricantes não se comunicam
- **Gestão de demanda manual**: sem controle automático, ocorrem picos que excedem limites contratados
- **Ausência de tarifação**: não há mecanismo de registro de sessão e cobrança dinâmica
- **Problemas de escalabilidade**: sistemas isolados não suportam múltiplos usuários e contratos
- **Desperdício energético**: sem otimização, a energia não é aproveitada eficientemente

Esses problemas resultam em:
- Multas por ultrapassagem de demanda
- Erros na cobrança
- Baixa experiência do usuário
- Operação ineficiente e não sustentável

---

## Proposta de Solução

**ChargeGrid Intelligence** é uma plataforma open source de gerenciamento centralizado de eletropostos que integra:

1. **Controle Inteligente de Demanda (DLM)**
   - Balanceamento automático de carga entre carregadores
   - Respeita limites contratados de potência
   - Evita multas e interrupções

2. **Protocolos Abertos (OCPP + MODBUS)**
   - Integração com qualquer carregador compatível
   - Comunicação em tempo real via WebSocket
   - Suporte a múltiplos fabricantes (GoodWe, Tesla, Wallbox, etc.)

3. **Tarifação e Pagamento Automatizados**
   - Registro de sessão com timestamp e energia consumida
   - Suporte a múltiplos modelos de cobrança:
     - Por kWh
     - Por tempo
     - Tarifação por horário (TOU - Time of Use)
     - Assinatura
   - Integração com gateways de pagamento

4. **Inteligência Artificial**
   - Previsão de demanda
   - Manutenção preditiva
   - Otimização de carregamento
   - Personalização por usuário

### Arquitetura

```
┌────────────────────────────────────────────────────────┐
│           INFRAESTRUTURA DE ELETROPOSTOS               │
│                                                        │
│     Painel Solar (GoodWe)                              │
│       │                                                │
│       ▼                                                │
│     Inversor GoodWe ──────────┐                        |
│  (MODBUS/REST)                │                        │
│                               │                        │
│      Carregador 1 ─────┐      │                        │
│      Carregador 2 ─────├─ OCPP ─┐                      │
│      Carregador N ─────┘        │                      │
│                                 ▼                      │
│                    ChargeGrid Intelligence             │
│                        (Backend Python)                │
│                               │                        │
│          ┌────────────────────┼────────────────┐       │
│          │                    │                │       │
│          ▼                    ▼                ▼       │
│    Tarifação              Pagamento       Monitoramento|
│    (Registro de           (Gateway)        (Dashboard) |
│     Sessão)                   |                |       │
│          │                    │                │       │
│          └────────────────────┼────────────────┘       │
│                               │                        │
│                         Usuário Final                  │
└────────────────────────────────────────────────────────┘
```

### Fluxo de Operação

1. Usuário conecta veículo ao carregador
2. Sistema autentica via RFID/App
3. ChargeGrid calcula potência disponível
4. Sessão de carregamento é iniciada
5. Energia é medida em tempo real
6. Tarifação automática é aplicada
7. Pagamento é processado
8. Dados são registrados para auditoria

---

## Tecnologias Utilizadas

| Camada | Tecnologia | Função |
|--------|-----------|--------|
| **Comunicação com Carregadores** | OCPP 1.6 / 2.0.1 (WebSocket) | Protocolo padrão de comunicação |
| **Integração de Energia** | MODBUS / GoodWe REST API | Leitura de potência e energia solar |
| **Backend** | Python 3.x | Lógica de controle e orquestração |
| **Autenticação** | Lógica Booleana (RFID + Pagamento + Cabo) | Segurança física do sistema |
| **Tarifação** | Sistema de Pricing Dinâmico | Cobrança por modelo selecionado |
| **Monitoramento** | Análise de Dados Estatística | Insights sobre uso e demanda |

### Por que essas tecnologias?

- **OCPP**: Padrão aberto que funciona com 90% dos carregadores do mercado
- **Python**: Ideal para prototipagem rápida e machine learning
---

## Pilares Técnicos

### 1. Controle de Demanda

Evita ultrapassar limites contratados de potência através de algoritmo inteligente:

```
Limite Contratado: 50 kW
Potência Disponível: 50 kW

Cenário:
- Carregador 1: 11 kW em uso
- Carregador 2: 11 kW em uso
- Carregador 3: Fila (esperando)

Ação: Carregador 3 aguarda liberar potência ou reduz para 7 kW
```

### 2. Protocolos Abertos

Integração com qualquer carregador via OCPP:

- `BootNotification`: Carregador se registra no sistema
- `StartTransaction`: Inicia sessão de recarga
- `MeterValues`: Recebe leitura de energia
- `StopTransaction`: Finaliza sessão e gera fatura

### 3. Tarifação Dinâmica

Registro de todas as sessões para cobrança automatizada:

```json
{
  "session_id": "SES_001",
  "start_time": "2026-05-06T08:00:00Z",
  "end_time": "2026-05-06T10:30:00Z",
  "energy_delivered": 45.5,
  "model": "TOU",
  "rate_applied": 2.50,
  "cost": 113.75,
  "payment_status": "completed"
}
```

### 4. Inteligência Artificial

Machine learning para otimização contínua:

- Previsão de demanda (qual hora vai ter mais carregamentos)
- Manutenção preditiva (quando carregador vai falhar)
- Otimização de tarifa (quando cobrar mais ou menos)
- Personalização (sugestões para cada usuário)

---

## Sustentabilidade e Energias Renováveis

O ChargeGrid Intelligence integra sustentabilidade em 3 níveis:

### 1. Integração com Energia Solar (GoodWe)

- Prioriza energia solar no carregamento
- Reduz consumo de rede durante picos de geração
- Aplica tarifa reduzida para carregamentos com energia solar

**Impacto**: Até 80% de redução de consumo de rede em dias ensolarados

### 2. Eficiência de Potência

- Controle de demanda evita picos desnecessários
- Reduz uso de gerador backup
- Otimiza uso da infraestrutura existente

**Impacto**: Até 30% de redução nos custos energéticos

### 3. Redução de Emissões

| Métrica | Estimativa |
|---------|-----------|
| CO₂ evitado por veículo/ano | ~600 kg |
| Redução de energia da rede | Até 80% |
| Eficiência de carregamento | +40% |

---

## Contexto FIAP

A FIAP, como instituição comprometida com a sustentabilidade, possui um **EV Charger em seu campus**. O ChargeGrid Intelligence foi desenvolvido para ser testado e implantado em ambientes como esse, alinhando-se aos objetivos da instituição de:

- Mobilidade sustentável
- Gestão eficiente de energia
- Inovação em tecnologia verde
- Educação prática em sistemas inteligentes

---

## Equipe
```
| Nome | RM |
|--------|-----------|--------|
| Gabriel Barbosa Furin | 572941 |
| Gabriel de Almeida Santos | 569395 |
| Herbert Soares de Jesus | 571507 |
| Lucas Kiodi Moraca | 571004 |
| Renan Fracalossi Mano da Silva | 569610 |
```
