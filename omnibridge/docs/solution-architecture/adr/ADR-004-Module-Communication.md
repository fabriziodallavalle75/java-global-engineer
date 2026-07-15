# ADR-004 – Module Communication

**Produto:** OmniBridge
**Versão:** 1.0
**Status:** Accepted
**Tipo:** Estruturante

---

## Contexto

O OmniBridge será desenvolvido como um monólito modular orientado por domínio.

Cada módulo possui responsabilidades próprias e uma arquitetura interna composta por `api`, `application`, `domain` e `infrastructure`, conforme definido no ADR-003.

Para preservar as fronteiras entre os módulos, é necessário estabelecer como eles poderão se comunicar.

Sem uma estratégia clara, existe o risco de um módulo acessar diretamente entidades, serviços de domínio ou detalhes internos de outro módulo, aumentando o acoplamento e comprometendo a modularidade da solução.

---

## Questão Arquitetural

Como os módulos do OmniBridge devem se comunicar de forma a preservar suas fronteiras, manter baixo acoplamento e permitir a evolução futura da arquitetura?

---

## Alternativas Consideradas

### Acesso direto aos elementos internos dos módulos

Um módulo poderia utilizar diretamente entidades, serviços de domínio ou componentes internos pertencentes a outro módulo.

**Vantagens**

* Implementação simples.
* Menor quantidade inicial de contratos.
* Facilidade para compartilhar dados entre módulos.

**Desvantagens**

* Forte acoplamento entre módulos.
* Exposição da implementação interna.
* Alterações internas propagam impacto para outros módulos.
* Dificulta a evolução da arquitetura.
* Compromete o encapsulamento dos domínios.

---

### Comunicação exclusivamente síncrona

Os módulos comunicam-se apenas por chamadas diretas.

**Vantagens**

* Fluxo simples de compreender.
* Resposta imediata.
* Facilidade de depuração.

**Desvantagens**

* Acoplamento temporal.
* Dependência direta entre módulos.
* Menor flexibilidade para evolução.

---

### Comunicação exclusivamente assíncrona

Toda comunicação ocorre por meio de eventos.

**Vantagens**

* Baixo acoplamento temporal.
* Facilidade para adicionar novos consumidores.
* Melhor preparação para arquiteturas distribuídas.

**Desvantagens**

* Maior complexidade.
* Consistência eventual.
* Inadequada para casos de uso que exigem resposta imediata.
* Complexidade desnecessária para o contexto atual do produto.

---

### Comunicação híbrida por contratos públicos e eventos

Chamadas síncronas são realizadas exclusivamente por meio da API pública dos módulos.

Eventos são utilizados para comunicar fatos relevantes do domínio.

**Vantagens**

* Preserva as fronteiras entre módulos.
* Mantém baixo acoplamento.
* Utiliza comunicação síncrona apenas quando necessária.
* Utiliza eventos apenas quando agregam valor.
* Facilita a evolução futura da arquitetura.

**Desvantagens**

* Exige disciplina arquitetural.
* Introduz dois mecanismos de comunicação.
* Requer governança dos contratos públicos.

---

## Decisão

O OmniBridge adotará uma estratégia híbrida de comunicação entre módulos.

A comunicação síncrona será utilizada quando um caso de uso depender de uma resposta imediata.

A comunicação assíncrona será utilizada para comunicar fatos relevantes do domínio que possam ser processados independentemente.

Nenhum módulo poderá acessar diretamente elementos pertencentes às camadas `application`, `domain` ou `infrastructure` de outro módulo.

Toda comunicação síncrona entre módulos deverá ocorrer exclusivamente por meio da **API pública do módulo**.

---

## Comunicação Síncrona

Cada módulo deverá expor uma **API pública**, responsável por disponibilizar os contratos utilizados pelos demais módulos da aplicação.

Toda comunicação síncrona deverá ocorrer exclusivamente por meio dessa API.

Os módulos consumidores dependerão apenas dos contratos públicos, sem conhecer ou acessar elementos pertencentes às camadas `application`, `domain` ou `infrastructure` do módulo fornecedor.

Exemplo:

```text
Orders
   |
   v
CatalogApi
   |
   v
Catalog Application
   |
   v
Catalog Domain
```

A API pública deverá expor apenas as operações necessárias aos consumidores, preservando o encapsulamento das responsabilidades internas do módulo.

---

## Comunicação Assíncrona

Eventos serão utilizados quando um módulo precisar comunicar a ocorrência de um fato de negócio sem depender de uma resposta imediata.

Exemplo:

```text
Orders
   |
   v
OrderReceived
   |
   +----> Monitoring
   |
   +----> Notification
   |
   +----> Outros consumidores futuros
```

Os eventos deverão:

* Representar fatos já ocorridos.
* Possuir nomes no passado.
* Ser imutáveis.
* Não conhecer seus consumidores.
* Transportar apenas as informações necessárias.
* Possuir tratamento para duplicidade quando aplicável.

A tecnologia utilizada para publicação e consumo desses eventos será definida em um ADR específico.

---

## Regras Arquiteturais

A comunicação entre módulos deverá obedecer às seguintes regras:

* Toda comunicação síncrona ocorrerá exclusivamente por meio da API pública do módulo.
* Nenhum módulo poderá acessar diretamente elementos pertencentes às camadas `application`, `domain` ou `infrastructure` de outro módulo.
* A implementação interna de um módulo poderá evoluir livremente, desde que sua API pública permaneça compatível.
* Eventos representam fatos de negócio já ocorridos e não deverão ser utilizados para consultas síncronas.

---

## Justificativa

Chamadas síncronas e eventos atendem necessidades diferentes.

Operações que exigem resposta imediata devem utilizar contratos públicos bem definidos.

Eventos devem ser utilizados quando o objetivo for comunicar a ocorrência de um fato de negócio, permitindo que outros módulos reajam sem criar dependências diretas.

Essa abordagem protege as fronteiras dos módulos, reduz o acoplamento, facilita testes e prepara a solução para uma possível evolução futura para uma arquitetura distribuída.

---

## Consequências

### Positivas

* Fronteiras claras entre módulos.
* Comunicação baseada em contratos explícitos.
* Domínios protegidos de dependências indevidas.
* Redução do acoplamento entre módulos.
* Facilidade para evolução futura da arquitetura.
* Maior previsibilidade na comunicação entre componentes.

### Negativas

* Necessidade de manter contratos públicos.
* Exige disciplina arquitetural da equipe.
* Introduz dois modelos de comunicação.
* Eventos adicionam consistência eventual em alguns fluxos.

---

## Critérios para Escolha do Modelo de Comunicação

A comunicação síncrona deverá ser utilizada quando:

* O caso de uso depender de resposta imediata.
* For necessária consulta a outro módulo.
* Houver necessidade de validação antes da continuidade do fluxo.
* A operação depender diretamente do resultado obtido.

A comunicação assíncrona deverá ser utilizada quando:

* O produtor não precisar aguardar o consumidor.
* O evento puder possuir múltiplos consumidores.
* O processamento puder ocorrer posteriormente.
* O fluxo admitir consistência eventual.
* O objetivo for comunicar um fato de negócio.

---

## Critérios para Revisão

Esta decisão deverá ser revisada caso ocorra uma ou mais das seguintes situações:

* Crescimento significativo das capacidades de negócio.
* Evolução para uma arquitetura distribuída.
* Necessidade de novos padrões de comunicação.
* Complexidade excessiva na manutenção dos contratos públicos.
* Requisitos de negócio que exijam modelos diferentes de comunicação entre módulos.

Até que um desses cenários se concretize, toda comunicação entre módulos deverá seguir as regras estabelecidas neste ADR.
