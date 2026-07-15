# ADR-002 – Canonical Commerce Model

**Produto:** OmniBridge
**Versão:** 1.0
**Status:** Approved
**Tipo:** Estruturante

---

## Contexto

O OmniBridge foi concebido para integrar diferentes marketplaces e disponibilizar informações consolidadas para sistemas consumidores.

Cada marketplace possui seu próprio modelo de dados, nomenclaturas, regras e formatos para representar produtos, preços, estoques e pedidos.

Caso essas diferenças sejam propagadas para o núcleo da aplicação, os domínios de negócio passarão a depender diretamente das particularidades de cada integração, aumentando o acoplamento e dificultando a evolução da solução.

A arquitetura definida no ADR-001 estabelece uma organização orientada por domínio. Para preservar essa organização, torna-se necessário definir uma representação comum dos principais conceitos de negócio utilizados pela plataforma.

---

## Questão Arquitetural

Como representar internamente as informações provenientes de diferentes marketplaces de forma consistente, preservando a independência dos domínios em relação aos modelos externos?

---

## Alternativas Consideradas

### Utilizar diretamente os modelos dos marketplaces

Cada domínio trabalharia diretamente com os objetos retornados pelas APIs dos marketplaces.

**Vantagens**

* Não exige transformação de dados.
* Menor esforço inicial de implementação.

**Desvantagens**

* Forte acoplamento entre o domínio e as APIs externas.
* Inclusão de novos marketplaces tende a aumentar a complexidade do núcleo.
* Sistemas consumidores passam a depender de modelos específicos de cada integração.

---

### Manter modelos independentes para cada integração

Cada adaptador possuiria seu próprio modelo interno, sendo responsabilidade dos consumidores conhecer esses diferentes formatos.

**Vantagens**

* Isolamento parcial das integrações.
* Menor impacto sobre cada adaptador individualmente.

**Desvantagens**

* Ausência de uma linguagem comum entre os domínios.
* Aumento da complexidade para sistemas consumidores.
* Duplicação de conceitos de negócio.

---

### Adotar um Canonical Commerce Model

Todos os adaptadores traduzem seus modelos específicos para um conjunto comum de objetos de negócio utilizado internamente pela aplicação.

**Vantagens**

* Baixo acoplamento entre domínios e integrações.
* Linguagem de negócio única para toda a solução.
* Inclusão simplificada de novos marketplaces.
* Contratos estáveis para os sistemas consumidores.

**Desvantagens**

* Necessidade de mapeamentos entre modelos externos e internos.
* Evolução controlada do modelo canônico.
* Possível necessidade de tratar informações específicas que não possuam representação direta.

---

## Decisão

O OmniBridge adotará um **Canonical Commerce Model** como representação interna dos principais conceitos de negócio da plataforma.

Todos os adaptadores deverão traduzir os modelos específicos dos marketplaces para o modelo canônico antes que as informações sejam disponibilizadas aos domínios internos.

Os domínios e os sistemas consumidores trabalharão exclusivamente com o modelo canônico.

---

## Justificativa

O principal objetivo do modelo canônico é proteger o núcleo da aplicação das particularidades dos sistemas externos.

Essa abordagem preserva a independência dos domínios, reduz o acoplamento entre integrações e facilita a inclusão de novos marketplaces sem impactar o restante da solução.

Além disso, estabelece uma linguagem comum para todo o sistema, simplificando a comunicação entre módulos e oferecendo contratos consistentes aos sistemas consumidores.

---

## Consequências

### Positivas

* Domínios independentes dos modelos dos marketplaces.
* Linguagem de negócio compartilhada por toda a aplicação.
* Redução do acoplamento entre integrações e núcleo.
* Inclusão simplificada de novos marketplaces.
* Contratos estáveis para consumidores internos e externos.

### Negativas

* Necessidade de manter mapeamentos entre modelos externos e canônicos.
* Evolução do modelo exige governança.
* Informações específicas de determinados marketplaces poderão exigir mecanismos de extensão ou tratamento especializado.

---

## Critérios para Revisão

Esta decisão deverá ser revisada caso ocorra uma ou mais das seguintes situações:

* O modelo canônico deixe de representar adequadamente os conceitos de negócio da plataforma.
* O custo de manutenção dos mapeamentos passe a superar os benefícios do desacoplamento.
* Novos requisitos de negócio exijam múltiplos modelos canônicos especializados.
* Os sistemas consumidores passem a necessitar dos modelos nativos dos marketplaces como requisito de negócio.
* A evolução do produto demonstre que uma linguagem única deixou de atender às necessidades da solução.

Até que um desses cenários se concretize, esta decisão permanece válida e não deverá ser revisada apenas pela inclusão de novos marketplaces ou pela evolução das APIs externas.
