# Como Construir uma Arquitetura de Software

## Objetivo

Este documento descreve o processo utilizado para transformar uma arquitetura de solução em uma arquitetura de software pronta para implementação.

Seu objetivo não é ensinar uma tecnologia específica, mas apresentar um método de trabalho que permita organizar um software em domínios, módulos, contratos e componentes técnicos de forma consistente.

Ao final desta disciplina, o software deverá estar suficientemente detalhado para que a implementação possa ser iniciada com o menor número possível de decisões arquiteturais pendentes.

---

# O papel da Arquitetura de Software

A Arquitetura de Software representa a continuidade natural da Arquitetura de Soluções.

Enquanto a Arquitetura de Soluções responde:

> **Qual solução deve ser construída?**

A Arquitetura de Software responde:

> **Como essa solução será construída?**

Seu foco está na organização interna do software, preservando todas as decisões arquiteturais previamente estabelecidas.

---

# Entrada da disciplina

A Arquitetura de Software utiliza como ponto de partida os artefatos produzidos durante a Arquitetura de Soluções.

Em especial:

* Vision Document;
* Product Requirements Document (PRD);
* Architecture Overview;
* Architecture Decision Records (ADRs).

Esses documentos representam as restrições e decisões que orientarão todo o detalhamento técnico.

---

# O que a Arquitetura de Software produz

O objetivo desta disciplina não é escrever código.

Seu objetivo é produzir uma especificação técnica suficientemente detalhada para orientar toda a implementação.

Os principais artefatos são:

* Software Architecture Overview;
* Domain Overview;
* Module Specifications;
* API Specifications;
* Event Catalog;
* Business Flows.

Cada artefato acrescenta um novo nível de detalhamento à solução.

---

# Processo

O detalhamento da arquitetura segue uma sequência incremental.

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

Cada etapa utiliza como entrada os artefatos produzidos anteriormente.

Essa sequência reduz incertezas e evita decisões prematuras durante a implementação.

---

# Etapa 1 — Software Architecture Overview

O primeiro passo consiste em definir como o software será organizado.

Nesta etapa são apresentados:

* visão geral da estrutura do software;
* organização dos módulos;
* responsabilidades gerais;
* relações entre os principais componentes;
* princípios utilizados durante a implementação.

O objetivo é estabelecer uma visão técnica comum antes do detalhamento dos módulos.

---

# Etapa 2 — Domain Overview

Após compreender a estrutura geral do software, identificam-se os domínios de negócio.

Cada domínio deve possuir:

* responsabilidade claramente definida;
* limites bem estabelecidos;
* baixo acoplamento com os demais domínios.

O objetivo não é detalhar classes ou entidades, mas identificar as grandes áreas funcionais do software.

---

# Etapa 3 — Module Specifications

Cada domínio é detalhado em um documento próprio.

Esses documentos descrevem:

* responsabilidades;
* capacidades;
* casos de uso;
* APIs públicas;
* eventos publicados;
* eventos consumidos;
* persistência;
* dependências.

Essa etapa representa o principal detalhamento da arquitetura de software.

---

# Etapa 4 — API Specifications

Após a definição dos módulos, são especificados seus contratos públicos.

O objetivo desta etapa é definir:

* operações disponibilizadas;
* parâmetros;
* respostas;
* erros;
* regras de utilização.

A implementação das APIs ainda não faz parte desta disciplina.

---

# Etapa 5 — Event Catalog

Com os módulos e contratos definidos, torna-se possível identificar os eventos de negócio da solução.

Nesta etapa são definidos:

* catálogo de eventos;
* estrutura dos eventos;
* produtores;
* consumidores;
* regras de publicação;
* evolução dos contratos.

Os eventos representam fatos de negócio e complementam os contratos síncronos definidos anteriormente.

---

# Etapa 6 — Business Flows

Por último, são descritos os principais fluxos do sistema.

Esses fluxos demonstram como módulos, APIs e eventos trabalham em conjunto para atender aos casos de uso do produto.

O objetivo é validar que toda a arquitetura projetada atende corretamente às necessidades do negócio.

---

# O que evitar

Durante esta disciplina algumas práticas devem ser evitadas.

## Implementar antes de projetar

A implementação deve ser consequência da arquitetura.

Nunca o contrário.

---

## Misturar responsabilidades

Cada domínio deve possuir uma responsabilidade claramente definida.

Evite módulos excessivamente genéricos ou responsáveis por múltiplos contextos de negócio.

---

## Projetar baseado em tecnologia

A organização do software deve refletir o domínio do negócio.

Frameworks, bibliotecas e tecnologias são decisões de implementação.

---

## Excesso de detalhamento

O objetivo da Arquitetura de Software não é substituir o código.

Documente apenas aquilo que auxilia na implementação e na compreensão da solução.

---

# Relação com a Implementação

Ao concluir esta disciplina, todas as principais decisões estruturais do software deverão estar documentadas.

A implementação passará a concentrar-se na construção das funcionalidades, seguindo a arquitetura previamente definida.

Novas decisões arquiteturais deverão ser registradas por meio de ADRs sempre que alterarem princípios ou diretrizes estabelecidos pela Arquitetura de Soluções.

---

# Resultado Esperado

Ao final desta disciplina, a equipe deverá possuir uma arquitetura de software completa, composta por:

* visão geral da arquitetura de software;
* domínios claramente definidos;
* módulos especificados;
* contratos das APIs públicas;
* catálogo de eventos;
* fluxos de negócio.

Esses artefatos representam a ponte entre a Arquitetura de Soluções e a Engenharia de Software, permitindo que a implementação seja iniciada de forma consistente, previsível e alinhada às decisões arquiteturais do projeto.
