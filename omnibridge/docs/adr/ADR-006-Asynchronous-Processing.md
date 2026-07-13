# ADR-006 – Asynchronous Processing

**Produto:** OmniBridge
**Versão:** 1.0
**Status:** Proposed
**Tipo:** Tático

---

## Contexto

O OmniBridge integra-se com marketplaces que podem apresentar indisponibilidade temporária, limites de requisições, respostas lentas e falhas intermitentes.

Diversas operações do produto não precisam ser concluídas durante a requisição original, como:

* Processamento de notificações recebidas dos marketplaces.
* Importação e atualização de pedidos.
* Sincronização de produtos, preços e estoques.
* Comunicação de fatos de negócio entre módulos.
* Reprocessamento de integrações com falha.
* Distribuição de eventos para múltiplos consumidores.

O projeto também será utilizado como portfólio técnico para vagas internacionais de desenvolvimento Java sênior e arquitetura de soluções. Portanto, deverá demonstrar competências relacionadas a arquiteturas orientadas a eventos, mensageria, resiliência e processamento distribuído.

---

## Questão Arquitetural

Qual estratégia de processamento assíncrono oferece confiabilidade, escalabilidade, rastreabilidade e aderência ao domínio de integração do OmniBridge?

---

## Alternativas Consideradas

### Processamento exclusivamente síncrono

Todas as operações seriam executadas durante a requisição que iniciou o processamento.

**Vantagens**

* Implementação simples.
* Fluxos fáceis de acompanhar.
* Resultado imediato.

**Desvantagens**

* Dependência direta da disponibilidade dos marketplaces.
* Maior tempo de resposta.
* Baixa resiliência.
* Dificuldade para retries e reprocessamentos.
* Inadequado para múltiplos consumidores.

---

### Processamento assíncrono em memória

As operações seriam executadas por threads ou executores internos da aplicação, sem persistência externa.

**Vantagens**

* Baixa complexidade inicial.
* Não exige infraestrutura adicional.

**Desvantagens**

* Perda de trabalhos em reinicializações.
* Baixa rastreabilidade.
* Dificuldade para escalabilidade e reprocessamento.
* Inadequado para operações relevantes de integração.

---

### Fila persistente no PostgreSQL

As operações assíncronas seriam registradas no banco relacional e processadas por trabalhadores internos.

**Vantagens**

* Não exige nova infraestrutura.
* Permite persistência, retries e reprocessamento.
* Adequada a volumes reduzidos.

**Desvantagens**

* Compartilha recursos com o banco transacional.
* Exige implementação própria de concorrência e entrega.
* Escalabilidade limitada.
* Menor aderência a múltiplos consumidores e replay de eventos.
* Reduz o valor demonstrativo do projeto como arquitetura orientada a eventos.

---

### RabbitMQ

Utilização de um message broker baseado em filas e roteamento de mensagens.

**Vantagens**

* Adequado para filas de trabalho e distribuição de comandos.
* Suporte a acknowledgements, retries e dead-letter queues.
* Roteamento flexível.
* Menor complexidade conceitual para alguns fluxos.

**Desvantagens**

* Retenção e replay não são o foco principal da plataforma.
* Menor aderência a fluxos de eventos duráveis e múltiplos consumidores independentes.
* Histórico de eventos exige soluções complementares.

---

### Apache Kafka

Utilização de uma plataforma distribuída de streaming de eventos.

**Vantagens**

* Persistência e retenção dos eventos.
* Possibilidade de replay.
* Escalabilidade por partições e consumer groups.
* Suporte natural a múltiplos consumidores independentes.
* Ordenação por chave dentro da partição.
* Forte aderência ao domínio de integração e distribuição de eventos.
* Tecnologia amplamente utilizada em arquiteturas corporativas.

**Desvantagens**

* Maior complexidade operacional.
* Exige decisões sobre tópicos, partições, chaves e retenção.
* Necessidade de tratamento explícito de idempotência e compatibilidade de eventos.
* Pode ser excessivo para aplicações pequenas sem requisitos de integração relevantes.

---

## Decisão

O OmniBridge utilizará **Apache Kafka** como plataforma principal para processamento assíncrono e comunicação orientada a eventos.

O Kafka será utilizado para:

* Receber e distribuir eventos de integração.
* Comunicar fatos de negócio entre módulos quando não houver necessidade de resposta imediata.
* Processar sincronizações assíncronas.
* Permitir múltiplos consumidores independentes.
* Suportar reprocessamento por meio da retenção e do replay de eventos.

Chamadas que exigirem resposta imediata continuarão utilizando a API pública síncrona dos módulos, conforme definido no ADR-004.

---

## Modelo de Comunicação

```text
Marketplace
     |
     v
Marketplace Adapter
     |
     v
Kafka Topic
     |
     +--------> Integration Consumer
     |
     +--------> Orders Consumer
     |
     +--------> Monitoring Consumer
```

Eventos internos seguirão o fluxo:

```text
Módulo produtor
      |
      v
Kafka Topic
      |
      +--------> Consumidor A
      |
      +--------> Consumidor B
```

---

## Eventos

Os eventos deverão:

* Representar fatos já ocorridos.
* Possuir nomes no passado.
* Ser imutáveis.
* Não conhecer seus consumidores.
* Transportar apenas os dados necessários.
* Possuir identificador único.
* Possuir data e hora de ocorrência.
* Possuir versão de contrato.
* Possuir chave adequada para particionamento e ordenação.

Exemplos:

```text
MarketplaceNotificationReceived
OrderImported
ProductSynchronized
InventoryUpdated
IntegrationFailed
```

---

## Idempotência

A entrega de mensagens será tratada como **at-least-once**.

Todo consumidor deverá ser idempotente, considerando que uma mensagem poderá ser processada mais de uma vez.

Quando aplicável, deverão ser utilizadas chaves de idempotência, como:

```text
marketplace + notificationId
marketplace + externalOrderId
eventId
```

O processamento repetido não poderá produzir efeitos de negócio duplicados.

---

## Ordenação

Quando a ordem dos eventos for relevante, todas as mensagens relacionadas ao mesmo agregado deverão utilizar a mesma chave de partição.

Exemplos:

* Eventos do mesmo pedido utilizam o identificador do pedido.
* Eventos do mesmo produto utilizam o identificador do produto.
* Eventos da mesma integração utilizam o identificador da conexão.

Não será presumida ordenação global entre todas as mensagens.

---

## Retry e Dead-Letter Topics

Falhas temporárias deverão ser encaminhadas para tópicos de retry.

Após o esgotamento das tentativas, a mensagem deverá ser encaminhada para um dead-letter topic.

Exemplo:

```text
order-import
order-import-retry
order-import-dlt
```

As políticas de retry deverão:

* Possuir quantidade máxima de tentativas.
* Utilizar intervalo progressivo quando aplicável.
* Diferenciar falhas temporárias de falhas definitivas.
* Preservar a mensagem original e o contexto do erro.
* Permitir reprocessamento controlado.

Nenhuma mensagem relevante deverá ser descartada silenciosamente.

---

## Contratos de Eventos

Os contratos dos eventos deverão possuir evolução controlada.

Alterações deverão priorizar compatibilidade retroativa, evitando remoção ou alteração incompatível de campos já publicados.

A estratégia de serialização e gerenciamento de schemas será definida em ADR específico caso a complexidade dos contratos justifique essa decisão.

---

## Transações e Publicação Confiável

A persistência de uma alteração de negócio e a publicação do evento correspondente não deverão depender de duas operações independentes sem mecanismo de consistência.

Será adotado o padrão **Transactional Outbox** para casos em que uma alteração persistida precise resultar na publicação confiável de um evento.

O registro de negócio e o registro da outbox deverão ser gravados na mesma transação local.

Um publicador assíncrono será responsável por enviar os eventos da outbox ao Kafka.

```text
Transação local
     |
     +----> Alteração de negócio
     |
     +----> Registro na Outbox
                    |
                    v
             Outbox Publisher
                    |
                    v
                  Kafka
```

---

## Justificativa

O Kafka é aderente ao papel do OmniBridge como plataforma de integração e distribuição de informações entre marketplaces e sistemas consumidores.

A retenção, o replay, os consumer groups e a capacidade de múltiplos consumidores oferecem suporte à evolução do produto e aos fluxos de integração previstos.

Embora introduza maior complexidade do que soluções internas ou baseadas no banco de dados, essa complexidade é justificada pelos requisitos de integração, resiliência, rastreabilidade e pelo objetivo do projeto de demonstrar práticas utilizadas em arquiteturas corporativas modernas.

---

## Consequências

### Positivas

* Processamento assíncrono confiável.
* Desacoplamento temporal entre produtores e consumidores.
* Retenção e replay de eventos.
* Escalabilidade dos consumidores.
* Suporte a múltiplos consumidores independentes.
* Melhor aderência à evolução futura do produto.
* Demonstração prática de arquitetura orientada a eventos.

### Negativas

* Maior complexidade de infraestrutura e operação.
* Necessidade de monitorar brokers, tópicos, consumidores e atrasos.
* Exige tratamento de idempotência.
* Exige governança dos contratos de eventos.
* Consistência eventual em parte dos fluxos.
* Necessidade de implementar retry, DLT e Transactional Outbox.

---

## Regras Arquiteturais

* Kafka será utilizado apenas em fluxos que realmente admitam processamento assíncrono.
* Operações que exigirem resposta imediata utilizarão APIs públicas síncronas.
* Eventos deverão representar fatos de negócio já ocorridos.
* Todos os consumidores deverão ser idempotentes.
* A ordenação será garantida apenas dentro da mesma chave de partição.
* Falhas deverão utilizar políticas explícitas de retry e dead-letter topic.
* Nenhuma mensagem relevante poderá ser silenciosamente descartada.
* Alterações de negócio que originem eventos utilizarão Transactional Outbox quando for necessária publicação confiável.
* O domínio não dependerá diretamente das APIs ou classes do Kafka.
* A infraestrutura de mensageria permanecerá encapsulada na camada `infrastructure`.

---

## Critérios para Revisão

Esta decisão deverá ser revisada caso ocorra uma ou mais das seguintes situações:

* A complexidade operacional do Kafka superar os benefícios para o produto.
* O volume de eventos permanecer reduzido e não justificar sua manutenção.
* Os fluxos passarem a ser predominantemente baseados em filas de comandos com requisitos mais aderentes a outro broker.
* Novos requisitos exigirem padrões de entrega, roteamento ou latência não atendidos adequadamente.
* A infraestrutura disponível não suportar a operação confiável da plataforma.
* A evolução da arquitetura exigir outra tecnologia de mensageria.

Até que um desses cenários se concretize, o Kafka será a plataforma padrão para processamento assíncrono e distribuição de eventos do OmniBridge.
