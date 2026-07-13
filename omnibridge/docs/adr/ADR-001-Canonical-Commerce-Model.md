# ADR-001 – Canonical Commerce Model

**Produto:** OmniBridge
**Versão:** 1.0
**Status:** Accepted

---

## Contexto

Cada marketplace possui seus próprios modelos de dados, nomenclaturas, estruturas, regras e formatos para representar produtos, preços, estoques e pedidos.

Caso essas diferenças sejam propagadas diretamente para os domínios internos e sistemas consumidores, o OmniBridge ficará fortemente acoplado às APIs externas.

Esse acoplamento aumentaria a complexidade da solução, dificultaria a inclusão de novos marketplaces e obrigaria os sistemas consumidores a conhecerem as particularidades de cada canal.

---

## Decisão

O OmniBridge adotará um **Canonical Commerce Model** para representar internamente os principais conceitos de comércio digital.

Os adaptadores de marketplace serão responsáveis por traduzir os modelos externos para o modelo canônico e, quando necessário, realizar a tradução no sentido inverso.

Os domínios internos e os sistemas consumidores não dependerão diretamente dos formatos específicos dos marketplaces.

No MVP, o modelo canônico contemplará:

* Produto.
* Preço.
* Estoque.
* Pedido.

---

## Fluxo Conceitual

```text
Modelo do Marketplace
          |
          v
Marketplace Adapter
          |
          v
Canonical Commerce Model
          |
          v
Domínios do OmniBridge
          |
          v
Sistemas Consumidores
```

---

## Alternativas Consideradas

### Utilizar diretamente o modelo de cada marketplace

Cada domínio e sistema consumidor trabalharia diretamente com os objetos fornecidos pelas APIs externas.

Essa alternativa foi descartada por aumentar o acoplamento e dificultar a inclusão de novos canais.

### Criar um modelo específico para cada marketplace dentro do núcleo

O núcleo do OmniBridge manteria representações próprias para Mercado Livre, Amazon, Shopee e outros canais.

Essa alternativa foi descartada porque espalharia particularidades externas pelo sistema e aumentaria a duplicação de lógica.

### Utilizar um modelo canônico compartilhado

Os formatos externos são traduzidos para uma representação interna comum.

Essa foi a alternativa escolhida por preservar a independência do núcleo e oferecer contratos estáveis aos sistemas consumidores.

---

## Consequências Positivas

* Redução do acoplamento com marketplaces.
* Inclusão simplificada de novos conectores.
* Contratos estáveis para sistemas consumidores.
* Padronização das informações consolidadas.
* Isolamento das particularidades externas nos adaptadores.
* Maior testabilidade dos domínios internos.

---

## Consequências Negativas

* Necessidade de implementar e manter mapeamentos entre modelos externos e canônicos.
* Possibilidade de perda de informações específicas de determinados marketplaces.
* Necessidade de evolução controlada do modelo canônico.
* Risco de criação de um modelo excessivamente genérico ou complexo.

---

## Diretrizes

* O modelo canônico deverá representar conceitos de negócio, não estruturas específicas de APIs externas.
* Campos exclusivos de um marketplace não deverão ser adicionados ao núcleo sem justificativa de negócio.
* Os adaptadores serão responsáveis pela tradução entre modelos externos e canônicos.
* A evolução do modelo deverá preservar compatibilidade sempre que possível.
* Extensões específicas poderão ser consideradas quando uma informação externa não puder ser representada adequadamente sem comprometer o núcleo.

---

## Impactos Futuros

Esta decisão influenciará:

* A estrutura dos adaptadores.
* Os contratos entre os domínios.
* As APIs disponibilizadas aos sistemas consumidores.
* A estratégia de persistência.
* O versionamento dos objetos de negócio.
* A inclusão de novos marketplaces e ERPs.
