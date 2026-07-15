# Software Architecture

## Objetivo

Esta disciplina reúne os artefatos produzidos durante o detalhamento da arquitetura de software.

Seu objetivo é transformar a arquitetura da solução em uma arquitetura de software pronta para implementação, definindo a organização interna do sistema, seus domínios, módulos, contratos e demais componentes técnicos.

Nesta etapa, as grandes decisões arquiteturais já foram tomadas pela disciplina de **Solution Architecture**. O foco passa a ser detalhar essas decisões de forma consistente, preservando os princípios arquiteturais estabelecidos e preparando a solução para ser implementada.

Ao final desta disciplina, a equipe deverá possuir uma arquitetura suficientemente detalhada para iniciar o desenvolvimento do software.

---

## Papel do Arquiteto de Software

O Arquiteto de Software é responsável por transformar a arquitetura da solução em uma arquitetura técnica implementável.

Seu principal objetivo é responder à seguinte pergunta:

> **Como a solução será construída?**

Para isso, define a organização interna do software, seus domínios, módulos, contratos, modelos e padrões técnicos, garantindo que a implementação permaneça aderente às decisões arquiteturais previamente estabelecidas.

Seu foco está na estrutura do software, e não na implementação de funcionalidades específicas.

---

## Relação com a Arquitetura de Soluções

A disciplina de **Software Architecture** utiliza como entrada os artefatos produzidos pela disciplina de **Solution Architecture**.

Em especial:

* Vision Document
* Product Requirements Document (PRD)
* Architecture Overview
* Architecture Decision Records (ADRs)

Esses documentos estabelecem as decisões estratégicas da solução.

A Arquitetura de Software detalha essas decisões, sem modificá-las.

Caso uma nova decisão arquitetural seja necessária durante o detalhamento, ela deverá ser registrada por meio de um novo ADR antes da continuidade do trabalho.

---

## Principais Entregáveis

Os principais artefatos produzidos por esta disciplina são:

* **Software Architecture Overview** — Visão geral da organização do software.
* **Domain Overview** — Identificação dos domínios e suas responsabilidades.
* **Module Specifications** — Especificação técnica de cada módulo.
* **API Specifications** — Definição das APIs públicas dos módulos.
* **Event Catalog** — Catálogo dos eventos de negócio utilizados pela solução.
* **Business Flows** — Fluxos funcionais e técnicos entre módulos e integrações.

Dependendo da complexidade da solução, outros artefatos poderão ser adicionados ao longo do projeto.

---

## Limites da Disciplina

A Arquitetura de Software define **como a solução será organizada internamente**.

Ela não define:

* regras de negócio específicas;
* implementação das funcionalidades;
* detalhes de código;
* estratégias de testes automatizados;
* pipelines de integração contínua;
* infraestrutura operacional.

Esses aspectos pertencem às disciplinas posteriores do Playbook.

---

## Processo

A disciplina segue um processo incremental de refinamento.

```text
Software Architecture Overview

↓

Domain Overview

↓

Module Specifications

↓

API Specifications

↓

Event Catalog

↓

Business Flows
```

Cada artefato reduz incertezas e acrescenta novos níveis de detalhamento à arquitetura, preparando o software para sua implementação.

---

## Princípios

Durante esta disciplina são adotados os seguintes princípios:

* Preservar as decisões estabelecidas pela Arquitetura de Soluções.
* Modelar o software a partir dos domínios de negócio.
* Definir responsabilidades claras para cada módulo.
* Projetar contratos antes da implementação.
* Minimizar o acoplamento entre componentes.
* Documentar apenas o necessário para orientar a implementação.
* Permitir evolução incremental da arquitetura.

---

## Resultado Esperado

Ao concluir esta disciplina, a equipe deverá possuir uma arquitetura de software completa, contendo:

* organização dos domínios;
* especificação dos módulos;
* contratos das APIs públicas;
* catálogo de eventos;
* fluxos de negócio;
* definições suficientes para iniciar a implementação.

Esses artefatos servirão como entrada para a disciplina de **Software Engineering**, onde a arquitetura será transformada em software executável.
