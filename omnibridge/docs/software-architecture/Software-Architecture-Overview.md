# Software Architecture Overview

**Produto:** OmniBridge
**Versão:** 0.1
**Status:** Draft

---

## 1. Objetivo

Definir como a arquitetura da solução do OmniBridge será materializada na organização do software.

Este documento apresenta os módulos da aplicação, suas responsabilidades, seus limites e as regras gerais de dependência e comunicação.

O detalhamento dos casos de uso, APIs, eventos e modelos de domínio será realizado em documentos específicos.

---

## 2. Referências Arquiteturais

A arquitetura de software deverá respeitar as decisões registradas nos seguintes documentos:

* Vision Document.
* Product Requirements Document.
* Architecture Overview.
* ADR-001 — Architectural Style.
* ADR-002 — Canonical Commerce Model.
* ADR-003 — Internal Module Architecture.
* ADR-004 — Module Communication.
* ADR-005 — Persistence Strategy.
* ADR-006 — Asynchronous Processing.
* ADR-007 — Authentication Strategy.

---

## 3. Organização Geral

O OmniBridge será implementado como um **monólito modular orientado por domínio**.

A aplicação será organizada inicialmente nos seguintes módulos:

```text
omnibridge
├── identity
├── integration
├── catalog
├── orders
└── monitoring
```

Cada módulo possuirá responsabilidades próprias, dados sob sua propriedade e uma API pública para comunicação síncrona.

A comunicação assíncrona ocorrerá por eventos publicados no Apache Kafka.

---

## 4. Estrutura Interna dos Módulos

Cada módulo será organizado da seguinte forma:

```text
<module>
├── api
├── application
├── domain
└── infrastructure
```

### `api`

Expõe os contratos públicos do módulo.

Poderá conter:

* Controllers.
* DTOs de entrada e saída.
* Fachadas e interfaces públicas.
* Consumidores de mensagens que representem entradas do módulo.

### `application`

Implementa e coordena os casos de uso.

É responsável por:

* Orquestrar fluxos.
* Coordenar objetos de domínio.
* Controlar transações.
* Utilizar contratos de persistência e integração.

Não deverá concentrar regras centrais do negócio.

### `domain`

Representa o núcleo de negócio do módulo.

Poderá conter:

* Entidades.
* Agregados.
* Value Objects.
* Serviços de domínio.
* Eventos de domínio.
* Contratos necessários ao domínio.

Não dependerá de Spring, Kafka, banco de dados, HTTP ou outras tecnologias externas.

### `infrastructure`

Implementa os detalhes técnicos do módulo.

Poderá conter:

* Persistência.
* Clientes de sistemas externos.
* Produtores de mensagens.
* Configurações técnicas.
* Implementações dos contratos definidos nas demais camadas.

---

## 5. Módulos

### 5.1 Identity

Responsável pela autenticação e autorização dos usuários e sistemas consumidores do OmniBridge.

Principais responsabilidades:

* Autenticar usuários.
* Emitir e validar tokens de acesso.
* Manter perfis e permissões.
* Proteger as APIs da plataforma.

Não será responsável pela autenticação do OmniBridge perante marketplaces.

---

### 5.2 Integration

Responsável pelas conexões e pela comunicação com marketplaces e outros sistemas externos.

Principais responsabilidades:

* Gerenciar conexões externas.
* Coordenar sincronizações.
* Administrar credenciais dos marketplaces.
* Acionar adaptadores.
* Receber notificações externas.
* Traduzir informações externas para o modelo canônico.
* Publicar eventos relacionados às integrações.

As particularidades de cada marketplace permanecerão restritas aos respectivos adaptadores.

---

### 5.3 Catalog

Responsável pelo estado atual das informações de catálogo consolidadas pelo OmniBridge.

Principais responsabilidades:

* Manter produtos consolidados.
* Manter preços atuais.
* Manter posições atuais de estoque.
* Disponibilizar informações de catálogo por sua API pública.
* Processar eventos relacionados ao catálogo.

O módulo trabalhará exclusivamente com representações canônicas.

---

### 5.4 Orders

Responsável pelas informações históricas dos pedidos provenientes dos marketplaces.

Principais responsabilidades:

* Registrar pedidos.
* Registrar itens de pedidos.
* Preservar os dados da venda no momento em que ocorreu.
* Manter o estado conhecido dos pedidos.
* Disponibilizar pedidos consolidados por sua API pública.
* Processar e publicar eventos relacionados a pedidos.

O módulo não executará processos próprios de faturamento, logística ou gestão financeira.

---

### 5.5 Monitoring

Responsável pela visibilidade operacional das integrações e dos processamentos.

Principais responsabilidades:

* Registrar sincronizações.
* Registrar falhas de integração.
* Acompanhar o estado das conexões.
* Disponibilizar indicadores operacionais.
* Alimentar o dashboard de acompanhamento.
* Consumir eventos relevantes para monitoramento.

O módulo não deverá assumir responsabilidades de observabilidade da infraestrutura que pertençam à plataforma de operação.

---

## 6. Canonical Commerce Model

O modelo canônico representa os conceitos compartilhados de comércio digital utilizados pelo OmniBridge.

No MVP, abrangerá:

* Product.
* Price.
* Inventory.
* Order.

Os adaptadores serão responsáveis pela conversão entre os modelos externos e o modelo canônico.

```text
Modelo externo
      |
      v
Marketplace Adapter
      |
      v
Canonical Commerce Model
      |
      v
Módulo responsável
```

O modelo canônico não deverá conter campos específicos de um marketplace sem justificativa de negócio.

---

## 7. Dependências entre Módulos

As dependências deverão permanecer explícitas e seguir as APIs públicas dos módulos.

Visão inicial:

```text
Identity
   |
   └── protege o acesso às APIs

Integration
   |
   ├──> Catalog API
   ├──> Orders API
   └──> Monitoring API

Catalog
   └──> Monitoring API ou eventos

Orders
   └──> Monitoring API ou eventos

Monitoring
   └── não acessa elementos internos dos demais módulos
```

Nenhum módulo poderá acessar diretamente as camadas `application`, `domain` ou `infrastructure` de outro módulo.

---

## 8. Comunicação Síncrona

A comunicação síncrona ocorrerá exclusivamente por meio da API pública do módulo fornecedor.

Exemplo:

```text
Integration
      |
      v
  Orders API
      |
      v
Orders Application
      |
      v
Orders Domain
```

O consumidor conhecerá apenas os contratos públicos disponibilizados pelo módulo.

Controllers HTTP não serão utilizados como mecanismo interno de comunicação dentro do monólito.

---

## 9. Comunicação Assíncrona

A comunicação assíncrona será realizada por eventos publicados no Apache Kafka.

```text
Módulo produtor
       |
       v
 Business Event
       |
       v
 Apache Kafka
       |
   +---+---+
   |       |
   v       v
Consumer Consumer
```

Eventos serão utilizados para comunicar fatos de negócio quando não houver necessidade de resposta imediata.

Os consumidores deverão ser idempotentes.

A publicação confiável de eventos associados a alterações persistidas utilizará Transactional Outbox.

---

## 10. Persistência

O OmniBridge utilizará uma única instância PostgreSQL.

Cada módulo será proprietário exclusivo de suas tabelas.

Regras:

* Não haverá acesso direto às tabelas de outro módulo.
* Foreign keys serão permitidas somente dentro do mesmo módulo.
* Referências entre módulos serão lógicas.
* Dados históricos serão armazenados no contexto responsável por preservá-los.
* Flyway controlará as alterações do banco.
* Spring Data JPA e Hibernate serão utilizados na infraestrutura de persistência.

Compartilhar a infraestrutura do banco não significa compartilhar os modelos de dados.

---

## 11. Autenticação e Segurança

O módulo Identity protegerá o acesso dos usuários e sistemas consumidores ao OmniBridge.

O módulo Integration e seus adaptadores serão responsáveis pelos mecanismos necessários para autenticação perante sistemas externos.

```text
Consumidor
    |
    v
Identity / JWT
    |
    v
OmniBridge

OmniBridge
    |
    v
Marketplace Adapter
    |
    v
OAuth, API Key ou mecanismo externo
```

Credenciais e tokens serão tratados como segredos e não poderão ser expostos em logs, eventos ou contratos públicos.

Os domínios permanecerão independentes dos mecanismos técnicos de autenticação.

---

## 12. Regras de Dependência

A implementação deverá respeitar as seguintes regras:

* A camada `api` poderá utilizar a camada `application`.
* A camada `application` poderá utilizar a camada `domain`.
* A camada `infrastructure` poderá implementar contratos definidos pelo módulo.
* A camada `domain` não dependerá das demais camadas.
* Um módulo dependerá apenas da API pública de outro módulo.
* O domínio não dependerá de frameworks ou tecnologias externas.
* A infraestrutura não poderá expor seus modelos como contratos públicos.
* Eventos e APIs não deverão expor entidades internas de domínio ou persistência.

Representação conceitual:

```text
api
 |
 v
application
 |
 v
domain
 ^
 |
infrastructure
```

---

## 13. Estrutura Inicial de Pacotes

```text
com.omnibridge
├── identity
│   ├── api
│   ├── application
│   ├── domain
│   └── infrastructure
├── integration
│   ├── api
│   ├── application
│   ├── domain
│   └── infrastructure
├── catalog
│   ├── api
│   ├── application
│   ├── domain
│   └── infrastructure
├── orders
│   ├── api
│   ├── application
│   ├── domain
│   └── infrastructure
└── monitoring
    ├── api
    ├── application
    ├── domain
    └── infrastructure
```

Os diretórios deverão ser criados somente quando existirem responsabilidades concretas a serem implementadas.

---

## 14. Validação da Arquitetura

As fronteiras definidas neste documento deverão ser verificadas por testes arquiteturais automatizados.

Esses testes deverão validar, entre outras regras:

* O domínio não depende de infraestrutura.
* Um módulo não acessa áreas internas de outro.
* Controllers permanecem na camada `api`.
* Implementações de persistência permanecem em `infrastructure`.
* Dependências entre módulos ocorrem apenas por APIs públicas.

A ferramenta utilizada será escolhida durante a fase de Engenharia de Software.

---

## 15. Próximos Artefatos

O detalhamento da arquitetura seguirá esta ordem:

1. Domain Overview.
2. Module Specifications.
3. API Specifications.
4. Event Catalog.
5. Business Flows.

Esses documentos complementarão este overview sem repetir suas definições.
