# Como Construir um Product Requirements Document (PRD)

## Objetivo

O Product Requirements Document (PRD) descreve **o que o produto deve entregar**.

Enquanto o Vision Document explica por que o produto existe, o PRD transforma essa visão em um conjunto organizado de capacidades que orientarão o desenvolvimento do produto.

Neste momento ainda não são tomadas decisões de arquitetura ou implementação. O foco continua sendo o produto.

---

# Quando elaborar o PRD

O PRD deve ser iniciado somente após a aprovação do Vision Document.

Antes de começar, certifique-se de que as seguintes perguntas já foram respondidas:

* Qual problema o produto resolve?
* Quem é o usuário?
* Qual é o propósito do produto?
* Qual é o escopo inicial (MVP)?

Caso alguma dessas respostas ainda não esteja clara, volte ao Vision Document antes de prosseguir.

---

# Passo 1 – Revise o Vision Document

O primeiro passo é reler o Vision.

O objetivo é garantir que todos os requisitos descritos no PRD estejam alinhados ao propósito do produto.

Evite adicionar funcionalidades que não contribuam diretamente para o problema que o produto se propõe a resolver.

O PRD deve ser uma evolução natural do Vision.

---

# Passo 2 – Defina o escopo do produto

Descreva, de forma objetiva, o que faz parte da solução.

Esse escopo servirá como referência para todas as discussões futuras.

Lembre-se de que definir o que está fora do escopo também é importante.

Produtos sem limites claros tendem a crescer descontroladamente.

---

# Passo 3 – Identifique os stakeholders

Liste as pessoas, equipes ou sistemas que possuem interesse no produto.

Nem todos utilizarão diretamente a solução, mas todos podem influenciar seus requisitos.

Evite listas muito grandes.

Registre apenas os stakeholders relevantes para o contexto do projeto.

---

# Passo 4 – Defina as personas

As personas utilizadas no Vision normalmente permanecem válidas.

Caso necessário, complemente suas necessidades para facilitar a compreensão das funcionalidades que serão desenvolvidas.

O objetivo continua sendo manter o produto orientado às necessidades do usuário.

---

# Passo 5 – Descreva o cenário atual e o cenário futuro

Documente brevemente:

* Como o problema é resolvido atualmente.
* Como será resolvido após a implantação do produto.

Essa comparação ajuda a justificar as funcionalidades que serão desenvolvidas.

Evite detalhar processos internos ou fluxos técnicos.

---

# Passo 6 – Defina as capacidades do produto

Este é o capítulo mais importante do PRD.

Em vez de pensar em telas ou funcionalidades isoladas, pense nas capacidades que o produto oferecerá.

Pergunte:

> **O que o usuário será capaz de fazer utilizando este produto?**

Cada capacidade deve representar um conjunto coerente de funcionalidades relacionadas ao mesmo objetivo.

Evite capacidades muito genéricas ou excessivamente específicas.

---

# Passo 7 – Identifique as funcionalidades

Após definir cada capacidade, liste as funcionalidades necessárias para implementá-la.

Neste momento não é necessário escrever casos de uso, histórias de usuário ou requisitos detalhados.

Uma lista objetiva costuma ser suficiente.

Essas funcionalidades servirão de base para a construção do backlog.

---

# Passo 8 – Defina os principais objetos de negócio

Identifique quais informações o produto manipulará.

Esses objetos representam conceitos importantes do domínio e ajudarão posteriormente na definição da arquitetura.

Exemplos:

* Produto
* Pedido
* Cliente
* Estoque
* Preço

Não confunda objetos de negócio com modelos de banco de dados.

---

# Passo 9 – Defina o MVP

Liste apenas as capacidades e funcionalidades que farão parte da primeira entrega.

O MVP deve ser pequeno o suficiente para ser entregue rapidamente, mas completo o bastante para validar a proposta do produto.

Evite incluir funcionalidades futuras apenas porque parecem interessantes.

---

# Passo 10 – Revise e simplifique

Ao finalizar o documento, faça uma revisão completa.

Para cada seção, pergunte:

> **Esta informação ajuda alguém a entender o que o produto deve entregar?**

Caso contrário, considere removê-la.

O PRD deve permanecer objetivo e fácil de consultar.

---

# Boas Práticas

* Mantenha o foco no produto.
* Organize o documento por capacidades.
* Utilize linguagem de negócio.
* Escreva de forma objetiva.
* Defina claramente o MVP.
* Registre apenas informações relevantes.

---

# Erros Comuns

* Misturar requisitos de negócio com arquitetura.
* Escrever casos de uso detalhados no PRD.
* Organizar o documento por telas ou menus.
* Antecipar decisões tecnológicas.
* Criar um MVP grande demais.
* Duplicar informações já existentes no Vision Document.

---

# Resultado Esperado

Ao concluir o PRD, qualquer pessoa envolvida no projeto deverá compreender:

* O que o produto faz.
* Quais capacidades ele oferece.
* Quais funcionalidades compõem cada capacidade.
* Qual é o escopo do MVP.
* Quais são os principais objetos de negócio.

O PRD servirá como base para a definição da arquitetura, do backlog e do planejamento das próximas etapas do projeto.

O próximo passo será elaborar o **Architecture Overview**, responsável por definir como a solução será organizada para atender aos requisitos descritos neste documento.
