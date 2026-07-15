# Como Construir Architecture Decision Records (ADR)

## Objetivo

Este documento descreve o processo utilizado para construir os **Architecture Decision Records (ADRs)** do OmniBridge.

O objetivo não é ensinar um modelo específico de ADR, mas apresentar um processo de tomada de decisão arquitetural que possa ser aplicado em qualquer projeto de software.

Ao final desta etapa, o arquiteto deverá possuir um conjunto de decisões arquiteturais registradas, justificadas e alinhadas aos objetivos do produto.

---

# O papel dos ADRs

Durante o desenvolvimento de um software, diversas decisões arquiteturais precisam ser tomadas.

Algumas delas terão impacto apenas na implementação atual.

Outras influenciarão toda a evolução do produto.

Os ADRs existem para registrar essas decisões, documentando:

* o problema arquitetural;
* as alternativas avaliadas;
* a decisão adotada;
* a justificativa da escolha;
* suas consequências;
* quando essa decisão deverá ser revisada.

Mais importante do que registrar a tecnologia escolhida é registrar **o raciocínio que levou à decisão**.

---

# Quando criar um ADR

Nem toda decisão merece um ADR.

Um ADR deve ser criado quando a decisão:

* influencia a arquitetura da solução;
* impacta vários módulos ou equipes;
* possui alternativas viáveis;
* provavelmente será questionada no futuro;
* possui custo elevado para ser alterada.

Decisões de implementação local normalmente não justificam um ADR.

---

# O momento correto

Os ADRs são produzidos **após** a definição do produto e da visão geral da arquitetura.

A sequência adotada no OmniBridge foi:

```text
Vision Document

↓

Product Requirements Document

↓

Architecture Overview

↓

Architecture Decision Records
```

Essa ordem evita decisões prematuras e garante que cada ADR seja consequência das definições anteriores.

---

# Estruturantes x Táticos

Durante o desenvolvimento do OmniBridge, os ADRs foram divididos em duas categorias.

## ADRs Estruturantes

Definem a identidade arquitetural da solução.

Normalmente são poucos e raramente sofrem alterações.

Exemplos:

* estilo arquitetural;
* organização dos módulos;
* comunicação entre módulos;
* modelo canônico do domínio.

Esses ADRs servem como base para todas as demais decisões.

---

## ADRs Táticos

Tratam decisões específicas de implementação arquitetural.

Exemplos:

* persistência;
* processamento assíncrono;
* autenticação;
* observabilidade;
* versionamento de APIs.

Esses documentos podem evoluir ao longo da vida do produto conforme novos requisitos surgem.

---

# Processo utilizado

Cada ADR foi construído seguindo sempre o mesmo fluxo.

## 1. Identificar a decisão

O primeiro passo consiste em identificar qual problema arquitetural precisa ser resolvido.

A pergunta normalmente assume a forma:

> Como devemos resolver determinado aspecto da arquitetura?

O foco deve permanecer no problema, e não na tecnologia.

---

## 2. Levantar alternativas

Antes de escolher uma solução, todas as alternativas relevantes devem ser discutidas.

Cada alternativa deve apresentar vantagens e desvantagens.

O objetivo não é provar que uma alternativa está errada, mas compreender os impactos de cada escolha.

---

## 3. Escolher a estratégia

Somente após compreender o contexto e comparar as alternativas a decisão é tomada.

Nesse momento a tecnologia passa a ser consequência da estratégia arquitetural.

Exemplo:

Não decidir primeiro por PostgreSQL.

Decidir primeiro pela estratégia de persistência.

A tecnologia apenas materializa essa decisão.

---

## 4. Registrar a decisão

A decisão deve ser escrita de forma clara e objetiva.

Qualquer arquiteto que leia o documento deve compreender rapidamente:

* o que foi decidido;
* por que foi decidido;
* quais regras deverão ser respeitadas.

---

## 5. Registrar as consequências

Nenhuma decisão arquitetural é perfeita.

Toda escolha possui benefícios e limitações.

Documentar essas consequências torna futuras revisões muito mais objetivas.

---

## 6. Definir critérios para revisão

Arquitetura evolui.

Por isso, todo ADR deve indicar em quais situações a decisão deverá ser revisada.

Essa prática evita que decisões sejam tratadas como permanentes quando, na verdade, dependem do contexto do produto.

---

# Estrutura adotada

Todos os ADRs do OmniBridge seguem uma estrutura comum:

```text
Contexto

Questão Arquitetural

Alternativas Consideradas

Decisão

Justificativa

Consequências

Critérios para Revisão
```

Dependendo da natureza da decisão, podem existir seções adicionais, como:

* Regras Arquiteturais;
* Princípios;
* Modelo Conceitual;
* Diagramas;
* Fluxos.

Essas seções são utilizadas apenas quando agregam clareza à decisão.

---

# O que evitar

Durante a elaboração dos ADRs algumas práticas foram deliberadamente evitadas.

## Escolher tecnologia antes da estratégia

A tecnologia deve surgir como consequência da arquitetura.

Nunca como ponto de partida.

---

## Documentar detalhes prematuros

Um ADR não deve registrar decisões que ainda não precisam ser tomadas.

Sempre que possível, detalhes de implementação são adiados para o momento em que houver informação suficiente.

---

## Misturar arquitetura com implementação

O objetivo do ADR é explicar **por que** determinada decisão foi tomada.

Não ensinar como configurar uma ferramenta ou escrever código.

---

## Registrar apenas conclusões

Um ADR sem contexto perde grande parte do seu valor.

As alternativas consideradas e a justificativa da escolha são tão importantes quanto a decisão final.

---

# Convenção adotada

Os arquivos seguem o padrão:

```text
ADR-XXX-Decision-Name.md
```

Exemplos:

```text
ADR-001-Architectural-Style.md

ADR-002-Canonical-Commerce-Model.md

ADR-003-Internal-Module-Architecture.md

ADR-004-Module-Communication.md

ADR-005-Persistence-Strategy.md

ADR-006-Asynchronous-Processing.md
```

O nome do arquivo representa sempre **a decisão arquitetural**, e não a tecnologia escolhida.

---

# Lições Aprendidas

Durante a construção dos ADRs do OmniBridge algumas lições tornaram-se evidentes.

A principal delas foi que decisões arquiteturais devem ser tomadas na ordem correta.

Primeiro compreende-se o negócio.

Depois define-se a arquitetura.

Somente então escolhem-se as tecnologias.

Outra lição importante foi registrar apenas as decisões necessárias naquele momento.

Detalhes que não influenciam a arquitetura permanecem em aberto até que exista informação suficiente para justificá-los.

Esse processo manteve a documentação objetiva, coerente e alinhada à evolução natural do produto.

---

# Resultado Esperado

Ao concluir esta etapa, a equipe deverá possuir um conjunto de ADRs que:

* documente as principais decisões arquiteturais do projeto;
* explique o motivo de cada decisão;
* estabeleça regras arquiteturais claras;
* sirva como referência para toda a implementação;
* possa evoluir juntamente com o produto.

Mais do que registrar tecnologias, os ADRs devem preservar o processo de tomada de decisão arquitetural, permitindo que futuras evoluções ocorram de forma consciente e consistente.
