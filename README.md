# 🧪 Desafio Técnico – Desenvolvedor Java  
Birô de Crédito – Integração com Empresas Parceiras

Bem-vindo(a)!

Este desafio avalia sua capacidade de projetar e implementar **integrações críticas de ingestão de dados**, comuns em birôs de crédito, onde confiabilidade, consistência, custo e rastreabilidade são essenciais.

Você atuará como desenvolvedor de um **birô de crédito**, responsável por receber, integrar e consolidar **informações de dívidas enviadas por empresas parceiras**.

## Objetivo

Construir um serviço responsável por:

- Receber dados de dívidas de empresas parceiras
- Integrar com múltiplos parceiros externos
- Normalizar e consolidar informações financeiras
- Garantir idempotência e rastreabilidade
- Tratar falhas de integração de forma resiliente
- Disponibilizar os dados consolidados para consumo interno

## Contexto do Negócio

O birô de crédito mantém parcerias com diversas empresas (bancos, financeiras, telecom, utilities, varejo), que disponibilizam informações de dívidas de consumidores.

Cada parceiro possui:

- Padrões de API distintos
- SLA variável
- Regras próprias de envio
- Qualidade de dados heterogênea

O birô deve ingerir esses dados, garantir consistência e evitar duplicidade de registros.

## Fluxo Funcional Esperado

- Receber solicitação de ingestão ou consulta
- Consultar uma ou mais empresas parceiras
- Normalizar os dados recebidos
- Consolidar dívidas por consumidor
- Persistir os dados consolidados
- Publicar eventos internos para consumo downstream

## Restrições Reais do Cenário

- Chamadas a parceiros podem ser pagas
- Integrações podem falhar ou ficar indisponíveis
- Parceiros podem enviar dados duplicados ou inconsistentes
- Dados são altamente sensíveis (LGPD)
- Reprocessamentos não devem gerar duplicidade

## Entregáveis

O candidato deve entregar:

- Código-fonte funcional
- Desenho de arquitetura da solução
- Testes automatizados
- Instruções claras para execução do projeto

## Requisitos Técnicos

- Java 17 ou superior
- Spring Boot
- API REST
- Integrações externas simuladas (mock ou fake)
- Persistência simples (banco em memória é suficiente)

## Endpoint Principal

A API expõe o endpoint `POST /debts/ingestion` para ingestão de dados de dívidas a partir de empresas parceiras, com os seguintes payloads:

Request Body:
```json
{
  "partner": "UTILITIES_X",
  "document": "12345678900"
}
```

Response payload:
```json
{
  "id": "uuid",
  "contractId": "ABC123",
  "amount": 8500.75,
  "dueDate": "2024-11-10",
  "barcode": "836200000015123400481009876543210000123456789012",
  "paid": true
}
```

### Exemplos de payloads de parceiros a serem normalizados:

#### Parceiro 1

```json
{
  "idRequest": "uuid",
  "origin": "RETAIL_Z",
  "customer": {
    "cpf": "12345678900"
  },
  "debt": {
    "contract": "ABC123",
    "value": 8500.75,
    "expiration": "2024-11-10",
    "paid": false,
    "barcode": "34191790010104351004791020150008291070000010000"
  }
}
```

#### Parceiro 2

```json
{
  "requestId": "uuid",
  "partnerCode": "BANK_X",
  "document": {
    "type": "CPF",
    "number": "12345678900"
  },
  "loan": {
    "contractNumber": "ABC123",
    "outstandingAmount": 8500.75,
    "dueDate": "2026-01-26",
    "status": "paid",
    "barcode": "34191790010104351004791020150008291070000010000"
  }
}
```

#### Parceiro 3

```json
{
  "identificador": "uuid",
  "empresa": "UTILITY_A",
  "documento": "12345678900",
  "contrato": "ABC123",
  "valor": "8500.75",
  "data_vencimento": "26/01/2026",
  "status_pagamento": "open",
  "codigo_de_barras": "34191790010104351004791020150008291070000010000"
}
```

O endpoint deve ser idempotente, evitar registros duplicados e garantir rastreabilidade da origem dos dados.

## Regras de Negócio Mínimas

- Identificar a empresa parceira de origem
- Validar dados obrigatórios
- Normalizar formatos (datas, valores, status)
- Garantir idempotência por requestId ou chave de negócio
- Consolidar dívidas por CPF
- Persistir os dados consolidados
- Publicar o evento **DebtIngested**

## Requisitos Técnicos Obrigatórios

A solução deve contemplar explicitamente:
- Timeout configurável para chamadas a parceiros
- Retry com limite e backoff
- Circuit breaker por parceiro
- Idempotência garantida mesmo em reprocessamentos
- Cache para evitar chamadas repetidas
- Mascaramento de CPF em logs
- Separação clara entre core de negócio e integrações externas

## Testes

Espera-se que o projeto contenha:
- Testes unitários da lógica de consolidação
- Testes de integração com parceiros simulados
- Testes de falha (timeout, erro 5xx, payload inválido)
- Testes de idempotência e reprocessamento

## Diferenciais

Não obrigatórios, mas contam pontos:
- Suporte a múltiplos parceiros via strategy pattern
- Cache distribuído (Redis)
- Feature flag para ativar/desativar parceiros
- OpenTelemetry ou Micrometer
- Rate limiting por parceiro
- OpenAPI / Swagger
- Docker / Docker Compose