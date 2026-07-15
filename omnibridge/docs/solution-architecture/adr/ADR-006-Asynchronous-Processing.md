# ADR-006 – Asynchronous Processing

**Produto:** OmniBridge
**Versão:** 1.0
**Status:** Accepted
**Tipo:** Tático

---

## Contexto

O OmniBridge integra-se com marketplaces e outros sistemas externos que podem apresentar indisponibilidade temporária, limites de requisições, respostas lentas e falhas intermitentes.

Diversas operações do produto não precisam ser concluídas durante a requisição original, como sincronizações, integrações, notificações e distribuição de informações entre módulos.

Além disso, a arquitetura do OmniBridge foi concebida para permitir a evolução incremental da solução, preservando o baixo acoplamento entre seus módulos.

Para atender a esses requisitos, torna-se necessário definir uma estratégia de processamento assíncrono que seja confiável, escalável e aderente às necessidades atuais e futuras do produto.

---

## Questão Arquitetural

Como o OmniBridge deverá executar operações assíncronas de forma confiável, preservando o desacoplamento entre módulos e permitindo a evolução futura da arquitetura?

---

## Alternativas Consideradas

### Processamento exclusivamente síncrono

Todas as operações seriam executadas durante a requisição que iniciou o processamento.

**Vantagens**

* Implementação simples.
* Fluxo fácil de compreender.
* Resposta imediata.

**Desvantagens**

* Maior tempo de resposta.
* Forte dependência da disponibilidade dos sistemas externos.
* Baixa resiliência.
* Dificuldade para retries e reprocessamentos.
* Inadequado para múltiplos consumidores.

---

### Processamento assíncrono em memória

As operações seriam executadas por threads internas da aplicação.

**Vantagens**

* Baixa complexidade inicial.
* Não exige infraestrutura adicional.

**Desvantagens**

* Perda de trabalhos em caso de reinicialização.
* Baixa rastreabilidade.
* Escalabilidade limitada.
* Inadequado para operações críticas de integração.

---

### Fila persistente em banco de dados

As operações seriam registradas no banco relacional e processadas posteriormente.

**Vantagens**

* Persistência dos trabalhos.
* Não exige infraestrutura adicional.
* Suporte a retries e reprocessamentos.

**Desvantagens**

* Compartilha recursos com o banco transacional.
* Escalabilidade limitada.
* Exige implementação própria da infraestrutura de processamento.
* Menor aderência ao modelo orientado a eventos adotado pelo produto.

---

### Plataforma externa de mensageria

Utilização de uma plataforma especializada para publicação e consumo de eventos.

**Vantagens**

* Baixo acoplamento entre produtores e consumidores.
* Escalabilidade.
* Persistência dos eventos.
* Múltiplos consumidores independentes.
* Reprocessamento e replay.
* Melhor aderência ao domínio do OmniBridge.

**Desvantagens**

* Maior complexidade operacional.
* Necessidade de infraestrutura dedicada.
* Exige governança dos contratos de eventos.

---

## Decisão

O OmniBridge adotará uma estratégia de processamento assíncrono baseada no **Apache Kafka**.

O Kafka será utilizado para distribuir eventos de negócio entre módulos e integrações externas sempre que não houver necessidade de resposta imediata.

Operações que exigirem resposta síncrona continuarão sendo realizadas exclusivamente por meio das APIs públicas dos módulos, conforme definido no ADR-004.

---

## Princípios do Processamento Assíncrono

O processamento assíncrono do OmniBridge seguirá os seguintes princípios arquiteturais:

* Eventos representam fatos de negócio.
* Eventos são publicados somente após a conclusão da operação de negócio.
* Eventos não serão utilizados para consultas.
* Chamadas que exigirem resposta imediata utilizarão APIs públicas.
* Consumidores deverão ser idempotentes.
* Eventos não conhecerão seus consumidores.
* O domínio permanecerá independente da tecnologia de mensageria.

---

## Modelo Conceitual

```text
Business Operation
        │
        ▼
 Business Event
        │
        ▼
     Apache Kafka
        │
   ┌────┴────┐
   ▼         ▼
Consumer A Consumer B
```

A operação de negócio produz um fato relevante.

Esse fato é publicado em um tópico do Kafka e poderá ser processado por um ou mais consumidores independentes.

---

## Eventos

Os eventos deverão:

* Representar fatos já ocorridos.
* Ser imutáveis.
* Possuir identificador único.
* Possuir data e hora de ocorrência.
* Possuir versão de contrato.
* Conter apenas as informações necessárias ao processamento.

A definição do catálogo de eventos será realizada durante a modelagem funcional da solução.

---

## Idempotência

O processamento deverá considerar que uma mesma mensagem poderá ser entregue mais de uma vez.

Todo consumidor deverá ser idempotente.

Quando aplicável, deverão ser utilizadas chaves de idempotência para impedir efeitos duplicados de negócio.

---

## Política de Processamento

A estratégia de processamento deverá contemplar:

* Retry automático para falhas temporárias.
* Backoff progressivo entre tentativas.
* Dead Letter Topic para falhas definitivas.
* Reprocessamento manual quando necessário.
* Registro das falhas para auditoria.

Nenhuma mensagem relevante poderá ser descartada silenciosamente.

---

## Ordenação

Quando a ordem dos eventos for relevante, todas as mensagens relacionadas ao mesmo agregado deverão utilizar a mesma chave de particionamento.

Não será presumida ordenação global entre todos os eventos.

---

## Publicação Confiável

Alterações persistidas no banco de dados que originarem eventos deverão utilizar o padrão **Transactional Outbox**, garantindo consistência entre a alteração do negócio e a publicação do evento.

A implementação do mecanismo será realizada na camada de infraestrutura, permanecendo transparente ao domínio.

---

## Regras Arquiteturais

O processamento assíncrono deverá obedecer às seguintes regras:

* Eventos serão utilizados exclusivamente para comunicar fatos de negócio.
* Eventos não deverão ser utilizados para consultas entre módulos.
* Operações síncronas utilizarão exclusivamente APIs públicas.
* Consumidores deverão ser idempotentes.
* O domínio não dependerá diretamente do Apache Kafka.
* A infraestrutura de mensageria permanecerá encapsulada na camada `infrastructure`.
* Contratos de eventos deverão evoluir preservando compatibilidade sempre que possível.

---

## Justificativa

O Apache Kafka foi escolhido porque o domínio do OmniBridge é naturalmente orientado à propagação de fatos de negócio entre múltiplos consumidores independentes.

Sua capacidade de retenção dos eventos, replay, escalabilidade e desacoplamento entre produtores e consumidores oferece melhor aderência às necessidades arquiteturais do produto do que abordagens baseadas exclusivamente em filas de trabalho.

Embora introduza maior complexidade operacional, essa decisão prepara a solução para evoluções futuras sem comprometer a arquitetura definida para o MVP.

---

## Consequências

### Positivas

* Baixo acoplamento entre módulos.
* Processamento assíncrono confiável.
* Múltiplos consumidores independentes.
* Suporte a replay de eventos.
* Escalabilidade da infraestrutura de processamento.
* Preparação para futuras integrações.

### Negativas

* Maior complexidade operacional.
* Necessidade de monitoramento da infraestrutura de mensageria.
* Necessidade de governança dos contratos de eventos.
* Exige implementação de políticas de retry, DLT e idempotência.

---

## Critérios para Revisão

Esta decisão deverá ser revisada caso ocorra uma ou mais das seguintes situações:

* A complexidade operacional do Kafka deixe de ser justificada.
* Novos requisitos de negócio demandem outro modelo de processamento.
* O volume de eventos permaneça significativamente inferior ao esperado.
* A arquitetura evolua para um modelo que exija outra estratégia de mensageria.
* A plataforma escolhida deixe de atender aos requisitos de disponibilidade, desempenho ou escalabilidade.

Até que um desses cenários se concretize, o Apache Kafka será a plataforma padrão para o processamento assíncrono do OmniBridge.

---

## Síntese

O processamento assíncrono do OmniBridge será baseado em eventos de negócio publicados em Apache Kafka.

Eventos serão utilizados exclusivamente para comunicar fatos relevantes do domínio, preservando o desacoplamento entre módulos e permitindo a evolução incremental da solução.

Operações que exigirem resposta imediata continuarão utilizando exclusivamente as APIs públicas dos módulos, conforme definido no ADR-004.
