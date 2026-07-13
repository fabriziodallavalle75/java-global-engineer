# ADR-005 – Persistence Strategy

**Produto:** OmniBridge
**Versão:** 1.0
**Status:** Accepted
**Tipo:** Tático

---

## Contexto

O OmniBridge foi concebido como um Monólito Modular Orientado por Domínio, utilizando um Canonical Commerce Model como representação interna dos conceitos de negócio.

Cada módulo é responsável por seu próprio domínio e comunica-se com os demais exclusivamente por meio de APIs públicas ou eventos, conforme definido nos ADRs anteriores.

A estratégia de persistência deve preservar esses princípios arquiteturais, garantindo baixo acoplamento entre módulos, consistência dos dados e simplicidade operacional para o MVP.

---

## Questão Arquitetural

Qual estratégia de persistência melhor atende aos requisitos atuais do OmniBridge, preservando a independência entre módulos, a consistência dos dados e a simplicidade operacional?

---

## Alternativas Consideradas

### Banco relacional compartilhado

Uma única instância de banco de dados relacional, compartilhada por todos os módulos, mantendo a propriedade dos dados em nível de aplicação.

**Vantagens**

* Forte consistência transacional.
* Excelente suporte a consultas relacionais.
* Tecnologia consolidada.
* Simplicidade operacional.
* Adequado ao contexto do MVP.

**Desvantagens**

* Requer disciplina arquitetural para evitar dependências indevidas entre módulos.
* Pode transmitir a falsa percepção de compartilhamento dos dados.

---

### Banco independente por módulo

Cada módulo possui seu próprio banco de dados.

**Vantagens**

* Isolamento físico completo.
* Independência tecnológica.
* Facilita uma futura evolução para uma arquitetura distribuída.

**Desvantagens**

* Complexidade operacional elevada.
* Maior esforço de administração.
* Complexidade incompatível com o estágio atual do produto.

---

### Banco documental (NoSQL)

Persistência baseada em documentos.

**Vantagens**

* Flexibilidade para estruturas variáveis.
* Modelo natural para documentos completos.
* Evolução simples de estruturas pouco rígidas.

**Desvantagens**

* Menor aderência ao modelo canônico definido para o domínio.
* Maior responsabilidade da aplicação na garantia da integridade dos dados.
* Não apresenta vantagens significativas para os requisitos atuais do produto.

---

## Decisão

O OmniBridge adotará uma estratégia de persistência baseada em um **único banco de dados PostgreSQL**, utilizando persistência relacional por meio do **Spring Data JPA**, com **Hibernate** como implementação da especificação JPA.

As alterações estruturais do banco de dados serão gerenciadas utilizando **Flyway**.

Embora a infraestrutura de persistência seja compartilhada, cada módulo permanecerá proprietário exclusivo de seus dados.

---

## Estratégia de Persistência

A persistência deverá seguir as seguintes diretrizes arquiteturais:

* Utilizar uma única instância PostgreSQL para toda a aplicação.
* Organizar logicamente os dados por módulo.
* Cada módulo será proprietário exclusivo de suas tabelas.
* Foreign keys serão permitidas apenas entre tabelas pertencentes ao mesmo módulo.
* Nenhum módulo poderá consultar ou alterar diretamente tabelas pertencentes a outro módulo.
* O acesso aos dados de outro módulo deverá ocorrer exclusivamente por meio de sua API pública.

---

## Dados Compartilhados entre Domínios

Quando um módulo necessitar registrar informações pertencentes a outro domínio, deverá armazenar apenas os dados necessários ao seu próprio contexto de negócio.

Por exemplo, o módulo **Catalog** mantém o estado atual do produto:

```text
Product
- productId
- sku
- name
- currentPrice
- currentStock
```

O módulo **Orders** registra um retrato da venda realizada:

```text
OrderItem
- productId
- sku
- productName
- unitSalePrice
- quantity
```

Alterações posteriores realizadas no catálogo não deverão modificar informações históricas registradas nos pedidos.

---

## Justificativa

A decisão busca preservar a simplicidade operacional do MVP sem comprometer a independência entre os módulos.

Embora todos compartilhem a mesma infraestrutura de banco de dados, a propriedade dos dados permanece definida em nível arquitetural.

A utilização de PostgreSQL oferece forte suporte a transações, integridade referencial dentro de cada módulo, consultas relacionais e evolução controlada do esquema de dados.

A escolha da tecnologia é consequência dos requisitos atuais do produto e poderá ser revisada caso novas necessidades de negócio justifiquem outra estratégia de persistência.

---

## Consequências

### Positivas

* Baixa complexidade operacional.
* Tecnologia consolidada e amplamente adotada.
* Forte consistência transacional.
* Excelente suporte a consultas relacionais.
* Preservação da propriedade dos dados por módulo.
* Facilidade para evolução incremental.

### Negativas

* Compartilhamento da infraestrutura de banco exige disciplina arquitetural.
* Uma futura evolução para bancos independentes exigirá planejamento.
* A equipe deverá evitar dependências diretas entre tabelas de módulos distintos.

---

## Regras Arquiteturais

A persistência deverá obedecer às seguintes regras:

* Cada módulo é proprietário exclusivo de seus dados.
* Nenhum módulo poderá acessar diretamente tabelas pertencentes a outro módulo.
* Toda comunicação entre módulos ocorrerá exclusivamente por APIs públicas ou eventos.
* Dados históricos deverão representar o estado do negócio no momento em que foram registrados.
* Alterações em um domínio não deverão modificar registros históricos pertencentes a outro domínio.

---

## Critérios para Revisão

Esta decisão deverá ser revisada caso ocorra uma ou mais das seguintes situações:

* Crescimento significativo das capacidades ou dos requisitos de negócio.
* Necessidade de escalabilidade independente entre módulos.
* Evolução para uma arquitetura distribuída.
* Requisitos específicos de persistência que justifiquem bancos especializados.
* Volume ou características dos dados tornem inadequada a utilização de um único banco relacional.

Até que um desses cenários se concretize, a estratégia de persistência permanecerá baseada em um banco relacional único, preservando a independência lógica entre os módulos.
