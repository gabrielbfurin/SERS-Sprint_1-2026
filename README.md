# ⚡ SolarCharge Bridge
### Integração Inteligente entre Energia Solar GoodWe e Carregadores de Veículos Elétricos

> **EV Challenge 2026 — Sprint 8**  
> Conectando geração solar e mobilidade elétrica para um futuro mais sustentável.

---

## 🔴 Problema + Justificativa

Instalações com painéis solares e carregadores de veículos elétricos operam hoje de forma **completamente isolada**. O inversor solar não sabe que há um carro para carregar. O carregador não sabe que há energia solar disponível. O resultado é um paradoxo cotidiano:

> O carro elétrico — símbolo da sustentabilidade — sendo carregado com energia da rede, enquanto a energia solar gerada localmente é desperdiçada ou exportada por centavos.

**Por que isso é um problema real:**

- Carregadores de VE (7–22 kW) representam até **60% do consumo de pico** em eletropostos comerciais
- Sem integração, o carregamento ocorre na rede mesmo com excedente solar disponível, gerando **picos de demanda desnecessários** e aumentando custos operacionais
- Proprietários de frotas e operadores de eletropostos **pagam tarifa cheia** pela energia que poderia ser gerada localmente
- A rede elétrica absorve picos concentrados nos horários de maior afluência, aumentando a pressão sobre a infraestrutura
- Não há mecanismo de **registro de sessão e tarifação dinâmica** que considere a disponibilidade de energia solar
- O potencial de sustentabilidade de instalações fotovoltaicas em eletropostos **não é aproveitado**

Esse problema afeta qualquer operador de eletropostos, condomínio residencial, frota corporativa ou empresa com painéis solares GoodWe — um mercado que cresce exponencialmente no Brasil e no mundo.

**Contexto FIAP:**
A FIAP, como instituição comprometida com a sustentabilidade, possui um EV Charger em seu campus. O SolarCharge Bridge foi concebido para ser testado e implantado em ambientes como esse, alinhando-se ao compromisso da instituição com mobilidade sustentável e gestão eficiente de energia renovável.

---

## 💡 Proposta de Solução

**SolarCharge Bridge** é uma solução de software que cria uma ponte de comunicação inteligente entre o inversor solar GoodWe e o carregador de veículo elétrico, usando protocolos abertos para maximizar o uso de energia solar no carregamento do VE e registrar sessões de recarga para fins de auditoria e tarifação dinâmica.

### Como funciona:

```
┌──────────────────────────────────────────────────────────────┐
│           INSTALAÇÃO COMERCIAL OU RESIDENCIAL                │
│                                                              │
│  ☀️ Painel Solar (telhado do eletroposte/garagem)           │
│       │                                                      │
│       ▼                                                      │
│  🔧 Inversor GoodWe ──── API SEMS ────► 🧠 SolarCharge      │
│       │                                    Bridge            │
│       │                                       │              │
│  🏢 Consumo Local (escritório/recepção)       │ OCPP         │
│                                               ▼              │
│                                     🔌 Carregador VE (OCPP)  │
│                                               │              │
│                                               ▼              │
│                                 📊 Logger de Sessão          │
│                                 (Tarifação & Auditoria)      │
│                                               │              │
│                                               ▼              │
│                                          🚗 Veículo          │
└──────────────────────────────────────────────────────────────┘
```

### Lógica de decisão:

| Excedente Solar | Ação do Sistema |
|-----------------|-----------------|
| > 80% da potência do carregador | Carrega na potência máxima (tarifa solar) |
| 30% – 80% | Carrega na potência proporcional ao excedente (tarifa híbrida) |
| < 30% | Aguarda ou carrega na potência mínima configurada |
| Nenhum (noite/nublado) | Carrega da rede no horário de menor tarifa |

A verificação ocorre a cada **30 segundos**, ajustando a corrente em tempo real conforme a geração solar varia ao longo do dia.

### Registro de Sessão e Tarifação Dinâmica

O SolarCharge Bridge implementa um sistema robusto de logging que registra:

- **Início e fim da sessão** (timestamp)
- **Energia solar consumida** durante o carregamento (kWh)
- **Energia da rede consumida** durante o carregamento (kWh)
- **Porcentagem de energia renovável** usada naquela sessão
- **Custo total da sessão** baseado em tarifação dinâmica

Isso permite:
1. **Cobrança precisa** de usuários e frotas
2. **Auditoria** de consumo de energia renovável
3. **Políticas de incentivo** (tarifa mais baixa para carregamentos 100% solares)
4. **Relatórios de sustentabilidade** para operadores de eletropostos

---

## 🛠️ Tecnologias Utilizadas

| Camada | Tecnologia | Função |
|--------|-----------|--------|
| **Leitura Solar** | GoodWe SEMS API (REST) | Dados de geração, consumo e excedente em tempo real |
| **Controle do Carregador** | OCPP 1.6 (WebSocket) | Ajuste dinâmico da corrente de carregamento |
| **Backend** | Python 3.x | Lógica de integração, tomada de decisão e logging |
| **Banco de Dados** | PostgreSQL / SQLite | Armazenamento de sessões e histórico de tarifação |
| **Comunicação** | WebSocket + JSON | Protocolo OCPP entre backend e carregador |
| **Hardware compatível** | GoodWe HCA G2 (7–22 kW) | Carregador com suporte a OCPP |
| **Inversor compatível** | GoodWe ET / ES / EH | Inversores híbridos com API SEMS |
| **Containerização** | Docker | Implantação simples em qualquer ambiente |

### Por que GoodWe?

A GoodWe oferece uma **API REST aberta** (SEMS Portal) que fornece dados em tempo real de:
- Potência gerada pelo painel solar (W)
- Potência consumida pela instalação (W)
- Excedente exportado para a rede (W)
- Estado de carga da bateria (%, se instalada)

Esses dados são exatamente o que o SolarCharge Bridge precisa para tomar decisões inteligentes de carregamento e aplicar tarifação dinâmica baseada em disponibilidade de energia renovável.

---

## 🌱 Impactos

### Sustentabilidade

| Impacto | Estimativa |
|---------|-----------|
| Redução no consumo de energia da rede | Até **80%** do carregamento feito com energia solar |
| Redução de CO₂ por veículo/ano | ~**600 kg** de CO₂ evitados (vs. carregamento 100% da rede) |
| Redução de picos na rede elétrica | Carregamento distribui a carga nos horários de maior geração |
| Aproveitamento do excedente solar | De ~30% para até **90%** do excedente usado localmente |

### Energia Renovável

- O carregamento do VE passa a ser **100% movido por energia solar** nos dias ensolarados
- Em instalações com bateria GoodWe, o sistema prioriza: Solar → Bateria → Rede
- Reduz a dependência de usinas termelétricas e hidrelétricas nos horários de pico
- Contribui para a **descarbonização do transporte** de forma real e mensurável
- Alinha-se com os objetivos da FIAP de mobilidade sustentável

### Econômico

- Redução de **30–70% nos custos de energia** relacionada ao carregamento do VE
- Para operadores de eletropostos, redução de custos operacionais através de tarifação dinâmica
- Sem necessidade de trocar o carregador ou o inversor — integração por software
- ROI estimado em **menos de 6 meses** para usuários que carregam diariamente

### Escalabilidade

- A mesma solução funciona para **residências, condomínios, eletropostos comerciais e frotas corporativas**
- Compatível com qualquer carregador que suporte OCPP 1.6
- Pode ser expandido para **Vehicle-to-Grid (V2G)** com OCPP 2.1 (futuro)
- Pronta para integração em ambientes como o EV Charger da FIAP

---

## 🚀 Como Executar (Conceitual)

```bash
# 1. Clonar o repositório
git clone https://github.com/gabrielbfurin/SERS-Sprint_1-2026

# 2. Configurar credenciais
cp .env.example .env
# Preencher: SEMS_USER, SEMS_PASS, OCPP_SERVER_URL, DB_CONNECTION

# 3. Executar
docker-compose up
```

---

## 👥 Equipe

| Nome | RM | Função |
|------|----|--------|
| [Nome 1] | [RM] | Desenvolvimento & GitHub |
| [Nome 2] | [RM] | Pesquisa & Apresentação |

---

*EV Challenge 2026 — Sprint 8 | Entrega: 09/05/2026*
