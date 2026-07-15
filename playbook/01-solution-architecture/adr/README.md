# Architecture Decision Records (ADRs)

## Objetivo

Este diretório reúne os materiais relacionados à definição e documentação das principais decisões arquiteturais de um projeto de software.

O foco não está apenas nos documentos finais (ADRs), mas principalmente no **processo utilizado para chegar até cada decisão**.

Ao longo desta seção, são apresentados os princípios utilizados para identificar uma decisão arquitetural, avaliar alternativas, justificar escolhas e registrar seus impactos na evolução da solução.

---

## O que você encontrará aqui

Esta seção é composta por dois tipos de documentos.

### Guias

Os guias explicam **como construir ADRs**.

Seu objetivo é apresentar um processo de trabalho que possa ser reutilizado em qualquer projeto de software, independentemente da tecnologia adotada.

Esses documentos descrevem:

* quando um ADR deve ser criado;
* como identificar decisões arquiteturais;
* como avaliar alternativas;
* como registrar justificativas;
* como definir critérios de revisão.

---

### ADRs do Projeto

Os ADRs registram as decisões arquiteturais adotadas durante o desenvolvimento do OmniBridge.

Cada documento representa uma decisão específica, incluindo:

* contexto;
* alternativas consideradas;
* decisão adotada;
* justificativa;
* consequências;
* critérios para revisão.

Esses documentos servem como referência para toda a implementação da solução.

---

## Princípios Utilizados

A construção dos ADRs segue alguns princípios fundamentais.

* Documentar apenas decisões relevantes para a arquitetura.
* Avaliar alternativas antes de escolher uma solução.
* Fazer a tecnologia surgir como consequência da arquitetura.
* Registrar os motivos da decisão, e não apenas o resultado.
* Manter os documentos objetivos e fáceis de consultar.
* Revisar decisões sempre que o contexto do produto mudar.

---

## Estrutura Recomendada

Os ADRs do Playbook utilizam uma estrutura comum composta por:

```text
Contexto

Questão Arquitetural

Alternativas Consideradas

Decisão

Justificativa

Consequências

Critérios para Revisão
```

Dependendo da decisão, outras seções podem ser adicionadas, como:

* Regras Arquiteturais;
* Princípios;
* Modelo Conceitual;
* Diagramas;
* Fluxos.

Essas seções são utilizadas apenas quando contribuem para tornar a decisão mais clara.

---

## Convenção de Nomes

Os arquivos seguem a convenção:

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

O nome do arquivo representa sempre **a decisão arquitetural**, e não a tecnologia utilizada para implementá-la.

---

## Objetivo Final

Ao concluir esta etapa, a equipe deverá possuir um conjunto consistente de decisões arquiteturais capazes de orientar toda a implementação do produto.

Mais do que registrar tecnologias, os ADRs preservam o processo de tomada de decisão do arquiteto, facilitando a evolução da solução e tornando explícitos os motivos que levaram cada escolha a ser adotada.
