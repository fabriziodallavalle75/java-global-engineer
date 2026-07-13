# ADR-003 – Internal Module Architecture

**Produto:** OmniBridge
**Versão:** 1.0
**Status:** Accepted
**Tipo:** Estruturante

---

## Contexto

No ADR-001 foi definida a adoção de uma arquitetura de **Monólito Modular Orientado por Domínio**.

Essa decisão estabelece que o sistema será organizado em módulos de negócio independentes.

Entretanto, ainda é necessário definir como cada módulo será estruturado internamente, de forma que a organização do código preserve os princípios arquiteturais adotados pelo projeto.

A definição de uma estrutura comum facilita a compreensão da solução, reduz a curva de aprendizado e promove consistência entre os diferentes módulos do sistema.

---

## Questão Arquitetural

Como organizar internamente os módulos do OmniBridge de forma a preservar a separação de responsabilidades, proteger o domínio de negócio e facilitar a evolução da solução?

---

## Alternativas Consideradas

### Organização por camadas técnicas

```text
controllers/
services/
repositories/
```

**Vantagens**

* Simples de compreender.
* Bastante conhecida pela comunidade.
* Estrutura comum em projetos Spring Boot.

**Desvantagens**

* Mistura responsabilidades de diferentes domínios.
* Dificulta a evolução do sistema.
* Favorece o aumento do acoplamento entre funcionalidades.

---

### Organização por funcionalidades (Package by Feature)

```text
orders/
catalog/
integration/
```

**Vantagens**

* Mantém os elementos relacionados próximos.
* Favorece a organização por domínio.

**Desvantagens**

* Não define uma organização interna para cada módulo.
* Pode resultar em estruturas diferentes para funcionalidades semelhantes.
* Dificulta a padronização da solução.

---

### Organização por Domínio com Arquitetura Interna Padronizada

```text
orders/
├── api/
├── application/
├── domain/
└── infrastructure/
```

**Vantagens**

* Organização orientada por domínio.
* Separação clara de responsabilidades.
* Domínio independente de frameworks e tecnologias.
* Estrutura uniforme entre todos os módulos.
* Facilita testes, manutenção e evolução.

**Desvantagens**

* Exige maior disciplina arquitetural.
* Pode parecer mais complexa para projetos muito pequenos.

---

## Decisão

Cada domínio do OmniBridge será implementado como um módulo independente organizado na seguinte estrutura:

```text
<domain>/
├── api/
├── application/
├── domain/
└── infrastructure/
```

Cada diretório representa **uma responsabilidade arquitetural distinta** dentro do módulo.

### api

Responsável por expor os contratos públicos do módulo, utilizados por clientes externos ou pelos demais módulos da aplicação.

Exemplos:

* Controllers REST.
* DTOs de entrada e saída.
* Fachadas públicas.
* Consumidores de mensagens.

---

### application

Responsável pela implementação dos casos de uso da aplicação.

Suas principais responsabilidades são:

* Orquestrar fluxos.
* Coordenar entidades e serviços de domínio.
* Controlar transações.
* Invocar portas de persistência e integração.

A camada de aplicação não deverá concentrar regras de negócio.

---

### domain

Representa o núcleo do módulo.

Contém:

* Entidades.
* Value Objects.
* Agregados.
* Serviços de domínio.
* Eventos de domínio.
* Interfaces que representam contratos do domínio.

O domínio não deverá depender de frameworks, banco de dados, mensageria ou tecnologias externas.

---

### infrastructure

Responsável pelos detalhes técnicos necessários ao funcionamento do módulo.

Exemplos:

* Persistência.
* Clientes HTTP.
* Publicadores de eventos.
* Configurações do framework.
* Implementações das interfaces definidas pelo domínio.

---

## Justificativa

A estrutura adotada combina organização por domínio com uma arquitetura interna consistente para todos os módulos.

Essa abordagem preserva as fronteiras do domínio de negócio, facilita a localização do código, reduz o acoplamento e favorece a evolução incremental da solução.

Além disso, permite que aspectos técnicos permaneçam isolados na infraestrutura, protegendo o domínio e simplificando sua manutenção e seus testes.

---

## Consequências

### Positivas

* Estrutura uniforme para todos os módulos.
* Separação clara de responsabilidades.
* Domínio independente de frameworks.
* Maior facilidade para manutenção e testes.
* Código mais organizado e previsível.
* Menor acoplamento entre responsabilidades técnicas e de negócio.

### Negativas

* Estrutura inicial mais elaborada do que arquiteturas tradicionais.
* Exige disciplina para preservar as responsabilidades de cada camada.
* Pequenos módulos poderão conter diretórios com pouca implementação durante as primeiras versões.

---

## Critérios para Revisão

Esta decisão deverá ser revisada caso ocorra uma ou mais das seguintes situações:

* A estrutura deixe de representar adequadamente as responsabilidades dos módulos.
* Novos requisitos arquiteturais exijam uma organização diferente do código.
* O crescimento das capacidades de negócio justifique outra estratégia de modularização.
* A experiência prática demonstre aumento de complexidade ou redução da produtividade da equipe.
* A evolução da arquitetura torne desnecessária ou inadequada a separação atualmente adotada.

Até que um desses cenários se concretize, esta organização deverá ser utilizada por todos os módulos do OmniBridge, preservando consistência arquitetural em toda a solução.
