# ADR-001 – Architectural Style

**Produto:** OmniBridge
**Versão:** 1.0
**Status:** Approved
**Tipo:** Estruturante

---

## Contexto

O OmniBridge é uma plataforma de integração entre marketplaces e sistemas consumidores.

O produto será desenvolvido inicialmente por uma equipe reduzida, com um MVP focado na integração com o Mercado Livre e na consolidação de produtos, preços, estoques e pedidos.

Embora o escopo inicial seja limitado, a solução foi concebida para evoluir gradualmente, incorporando novos marketplaces, novos consumidores e novas capacidades de negócio.

A primeira decisão arquitetural consiste em definir o estilo arquitetural que servirá de base para toda a solução.

Essa decisão influenciará diretamente a organização do código, a definição dos módulos, a estratégia de testes, a evolução da arquitetura e diversas decisões técnicas futuras.

---

## Questão Arquitetural

Qual estilo arquitetural oferece o melhor equilíbrio entre simplicidade, organização por domínio, facilidade de evolução, baixo custo operacional e capacidade de crescimento para o contexto atual do OmniBridge?

---

## Alternativas Consideradas

| Critério                 | Monólito Tradicional | Monólito Modular | Microsserviços |
| ------------------------ | :------------------: | :--------------: | :------------: |
| Simplicidade             |           ✅          |         ✅        |        ❌       |
| Organização por domínio  |           ❌          |         ✅        |        ✅       |
| Baixo custo operacional  |           ✅          |         ✅        |        ❌       |
| Evolução incremental     |           ❌          |         ✅        |        ✅       |
| Complexidade operacional |         Baixa        |       Baixa      |      Alta      |
| Adequado ao MVP          |        Parcial       |        Sim       |       Não      |

### Monólito Tradicional

Uma única aplicação organizada predominantemente por camadas técnicas.

Apesar da simplicidade inicial, essa abordagem tende a aumentar o acoplamento entre funcionalidades à medida que o produto evolui, dificultando a preservação das fronteiras de negócio.

---

### Monólito Modular Orientado por Domínio

Uma única aplicação organizada em módulos de negócio com responsabilidades claramente definidas.

Essa abordagem mantém a simplicidade operacional de um monólito e, ao mesmo tempo, favorece alta coesão, baixo acoplamento e evolução incremental da solução.

---

### Microsserviços

Arquitetura composta por múltiplos serviços independentes.

Embora ofereça excelente isolamento e implantação independente, introduz uma complexidade operacional incompatível com o estágio atual do produto, exigindo infraestrutura, observabilidade distribuída, comunicação entre serviços e maior esforço de desenvolvimento.

---

## Decisão

O OmniBridge adotará uma arquitetura de **Monólito Modular Orientado por Domínio**.

Os domínios de negócio serão implementados como módulos independentes, preservando responsabilidades claras e contratos explícitos entre eles.

A solução será implantada inicialmente como uma única aplicação.

---

## Justificativa

A decisão busca o melhor equilíbrio entre simplicidade, organização arquitetural e capacidade de evolução.

O contexto atual do produto não apresenta requisitos que justifiquem a adoção de uma arquitetura distribuída. Por outro lado, a organização por módulos orientados por domínio permite preservar fronteiras claras entre as responsabilidades do sistema e reduzir o acoplamento entre suas partes.

Essa abordagem permite concentrar os esforços na construção do produto e na entrega de valor ao negócio, adiando decisões de maior complexidade operacional até que elas sejam efetivamente necessárias.

Além disso, a modularização favorece uma futura evolução da arquitetura caso novos requisitos de negócio passem a justificar uma distribuição física dos módulos.

---

## Consequências

### Positivas

* Arquitetura simples de desenvolver e operar.
* Organização alinhada ao domínio de negócio.
* Baixo custo operacional.
* Alta coesão entre responsabilidades.
* Baixo acoplamento entre módulos.
* Facilidade para testes e manutenção.
* Evolução incremental da solução.
* Possibilidade de evolução futura para uma arquitetura distribuída.

### Negativas

* Escalabilidade ocorre inicialmente para toda a aplicação.
* Os módulos compartilham o mesmo processo de execução.
* A separação entre módulos dependerá da disciplina arquitetural da equipe.
* Não existe implantação independente entre módulos.

---

## Critérios para Revisão

Esta decisão deverá ser revisada caso ocorra uma ou mais das seguintes situações:

* Crescimento significativo das capacidades ou dos requisitos de negócio.
* Inclusão de novos domínios que justifique uma reorganização arquitetural.
* Necessidade de implantações independentes entre módulos.
* Necessidade de escalabilidade independente para diferentes domínios.
* Crescimento da equipe que exija maior autonomia entre áreas do sistema.
* Requisitos distintos de disponibilidade entre módulos.
* Restrições operacionais que não possam ser atendidas por um monólito modular.

Até que um desses cenários se concretize, esta decisão permanece válida e não deverá ser revisada apenas por preferência tecnológica ou adoção de tendências arquiteturais.
