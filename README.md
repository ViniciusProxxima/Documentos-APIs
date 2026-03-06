# 📡 Proxxima Integration APIs

Documentação oficial das APIs utilizadas nas integrações internas da **Proxxima Telecom**.

Este repositório centraliza os endpoints utilizados para integração com diferentes serviços da infraestrutura do provedor.

---

# 📑 Sumário

- Arquitetura
- Autenticação
- 7AZ API
- Flashman API
- GCX API
- Geogrid / Zeus API
- Fluxos de Integração
- Boas Práticas

---

# 🧩 Arquitetura

As APIs documentadas neste repositório fazem parte do ecossistema de integração do provedor.

Fluxo geral de integração:

Cliente  
│  
▼  
Sistema / Bot / CRM  
│  
├── 7AZ → Financeiro  
├── GCX → Atendimento / Rede  
├── Flashman → Diagnóstico WiFi  
└── Geogrid → Geolocalização / Viabilidade  

Essas integrações permitem automações como:

- consulta automática de faturas
- diagnóstico de rede
- histórico de atendimento
- verificação de viabilidade técnica
- automação de suporte

---

# 🔐 Autenticação

Cada API possui um método de autenticação diferente.

| API | Tipo |
|----|----|
| 7AZ | API Key |
| Flashman | Basic Auth |
| GCX | Credenciais no Body |
| Geogrid | API Key |

---

# 💰 7AZ API

Base URL

https://api.7az.com.br/v2

---

## 📄 Listar Faturas

Retorna todas as faturas associadas a um CPF ou CNPJ.

Endpoint

GET /integrations/omnichannel/invoices

Parâmetros

| Nome | Tipo | Descrição |
|-----|-----|-----|
| txId | string | CPF ou CNPJ do cliente |

Exemplo de requisição

GET https://api.7az.com.br/v2/integrations/omnichannel/invoices?txId=40120343000104

Headers

X-API-Key: {API_KEY}

Exemplo de resposta

{
  "invoices": [
    {
      "id": 12345,
      "value": 120.50,
      "dueDate": "2026-03-10",
      "status": "open"
    }
  ]
}

---

## 💳 Faturas Disponíveis para Negociação

Retorna faturas elegíveis para renegociação.

Endpoint

GET /integrations/omnichannel/negotiations/invoices-available-for-negotiation

Exemplo

GET /negotiations/invoices-available-for-negotiation?txId={cpf}

---

## 💳 Dados de Pagamento

Retorna dados de pagamento de uma fatura.

Endpoint

GET /integrations/omnichannel/invoices/{invoiceId}/payment-data

Exemplo

GET /invoices/{invoiceId}/payment-data?customerId=40120343000104&withNegotiationLink=true

Parâmetros

| Parâmetro | Descrição |
|-----------|-----------|
| customerId | CPF ou CNPJ |
| withNegotiationLink | Retorna link de negociação |

---

# 📡 Flashman API

Base URL

https://flashman.proxxima.net/api/v3

---

## 📶 Site Survey do Dispositivo

Retorna redes WiFi detectadas pelo dispositivo.

Endpoint

GET /device/mac/{mac}/site-survey

Parâmetros

| Nome | Tipo | Descrição |
|----|----|----|
| mac | string | MAC Address do dispositivo |

Exemplo

GET https://flashman.proxxima.net/api/v3/device/mac/AA:BB:CC:DD:EE:FF/site-survey/?caseInsensitive=false

Headers

Authorization: Basic {BASE64}  
Cookie: SERVERID=s1  
Content-Type: application/json  

---

# 🧰 GCX API

Base URL

https://gcx.proxxima.net

---

## 📋 Eventos de Atendimento

Consulta histórico de eventos de um contrato.

Endpoint

POST /apiAtendimento.php

Body

{
  "usuario": "AUTENTICA",
  "senha": "curlatendimentosgx",
  "tipo": "eventos",
  "contract_id": "123456"
}

Parâmetros

| Campo | Tipo | Descrição |
|------|------|-----------|
| usuario | string | Usuário da API |
| senha | string | Senha da API |
| tipo | string | Tipo da consulta |
| contract_id | string | ID do contrato |

---

## 🏢 Consulta de Lojas

Retorna as lojas disponíveis por cidade.

Endpoint

GET /api_lojas.php

Exemplo

GET https://gcx.proxxima.net/api_lojas.php?cidade=Campina%20Grande

---

## 🧰 Consulta OLT / Slot / Porta

Retorna o último registro de um equipamento GPON.

Endpoint

GET /apiOltSlotPortaUltimoRegistro.php

Parâmetros

| Parâmetro | Tipo | Descrição |
|-----------|------|-----------|
| olt | string | Nome da OLT |
| slot | integer | Slot |
| porta | integer | Porta |

Exemplo

GET /apiOltSlotPortaUltimoRegistro.php?olt=OLT01&slot=0&porta=0

Headers

Authorization: Basic {BASE64}

---

# 🗺 Geogrid / Zeus API

Base URL

https://zeus.geogridmaps.com.br

---

## 📍 Converter Endereço em Coordenadas

Transforma endereço em latitude e longitude.

Endpoint

POST /bs/api/v3/integracao/endereco/cordenada

Headers

Accept: application/json  
Content-Type: application/json  
api-key: {API_KEY}

Body

{
  "endereco": "Rua das Flores",
  "numero": "123",
  "bairro": "Centro",
  "cidade": "Campina Grande",
  "estado": "PB"
}

---

## 📡 Consulta de Viabilidade

Consulta equipamentos dentro de um raio geográfico.

Endpoint

GET /bs/api/v3/viabilidade/raio

Exemplo

GET /viabilidade/raio?latitude={latitude}&longitude={longitude}&raio=600

Parâmetros

| Parâmetro | Descrição |
|-----------|-----------|
| latitude | Latitude |
| longitude | Longitude |
| raio | Raio da consulta em metros |

Parâmetros adicionais

consultarIndividual=S  
consultarPasta=N  
equipamentosAtendimento[]=S  
equipamentosAtendimento[]=N  
modoProjeto[]=S  
modoProjeto[]=N  
itens[]=grupoAcesso  
itens[]=terminal  

---

# 🔄 Fluxos de Integração

Principais fluxos automatizados:

1️⃣ **Consulta de Faturas**

Bot / CRM  
→ 7AZ API  
→ Retorno de faturas  
→ Exibição ao cliente

2️⃣ **Diagnóstico de WiFi**

Cliente relata problema  
→ Flashman API  
→ Site Survey  
→ Análise de interferência

3️⃣ **Histórico de Atendimento**

CRM / Bot  
→ GCX API  
→ Eventos de atendimento

4️⃣ **Consulta de Viabilidade**

Endereço informado  
→ Geogrid API  
→ Coordenadas  
→ Consulta de rede disponível

---

# ✅ Boas Práticas

Recomendações para utilização das APIs:

- Nunca expor **API Keys** em código público
- Utilizar **variáveis de ambiente**
- Implementar **timeout e retry**
- Monitorar endpoints via **Grafana**
- Criar **logs de integração**
- Utilizar **cache para consultas frequentes**

---

# 📚 Observações

Esta documentação reúne os principais endpoints utilizados nas integrações internas da **Proxxima Telecom**.

Sempre que novos serviços forem integrados, este documento deverá ser atualizado.
