# Como Construir um Architecture Overview

## Objetivo

O Architecture Overview é o documento que descreve **como a solução está organizada**.

Seu objetivo é apresentar a estrutura arquitetural do sistema de forma clara e objetiva, permitindo que qualquer membro da equipe compreenda rapidamente seus principais elementos.

O Architecture Overview não substitui documentos de detalhamento técnico, diagramas completos ou Architecture Decision Records (ADRs).

---

# Quando elaborar o Architecture Overview

O Architecture Overview deve ser iniciado após a conclusão do Product Requirements Document (PRD).

Antes de começar, certifique-se de que os seguintes pontos estejam definidos:

* O propósito do produto.
* O escopo do MVP.
* As capacidades do produto.
* Os principais objetos de negócio.

Sem essas informações, existe o risco de criar uma arquitetura orientada por tecnologia em vez de orientada pelo negócio.

---

# Passo 1 – Compreenda o domínio

Antes de pensar em componentes ou tecnologias, procure compreender como o negócio está organizado.

Pergunte:

> **Quais são os principais domínios do produto?**

Esses domínios servirão como base para toda a arquitetura.

Evite organizar a solução por funcionalidades, telas ou camadas técnicas.

---

# Passo 2 – Escolha um estilo arquitetural

Somente após compreender o domínio escolha o estilo arquitetural que melhor atende ao produto.

Essa decisão deve considerar:

* Complexidade do problema.
* Escalabilidade esperada.
* Facilidade de evolução.
* Custo operacional.
* Perfil da equipe.

A escolha deve ser consequência dos requisitos do produto e não da popularidade de uma tecnologia ou padrão arquitetural.

---

# Passo 3 – Identifique os principais domínios

Liste os domínios responsáveis pelas capacidades do produto.

Cada domínio deve possuir uma responsabilidade clara e representar um conceito importante do negócio.

Evite criar domínios excessivamente grandes ou muito específicos.

---

# Passo 4 – Identifique conceitos compartilhados

Alguns conceitos serão utilizados por diferentes domínios.

Esses conceitos não representam novos domínios, mas modelos compartilhados.

Quando isso ocorrer, registre esses conceitos separadamente.

No OmniBridge, por exemplo, adotou-se um **Canonical Commerce Model** para representar os principais objetos de negócio utilizados por toda a aplicação.

---

# Passo 5 – Defina os princípios arquiteturais

Registre apenas os princípios que realmente orientarão o desenvolvimento do sistema.

Exemplos:

* Organização por domínio.
* Baixo acoplamento.
* Alta coesão.
* Evolução incremental.
* Simplicidade.

Evite listas extensas de princípios que nunca serão utilizados durante o projeto.

---

# Passo 6 – Utilize diagramas para explicar a arquitetura

Sempre que possível, substitua texto por diagramas.

Um bom Architecture Overview normalmente contém apenas alguns diagramas de alto nível.

Os mais úteis costumam ser:

* Visão de contexto.
* Visão dos domínios.
* Visão das dependências.
* Fluxo conceitual.

Cada diagrama deve comunicar uma ideia específica.

Evite diagramas excessivamente detalhados.

---

# Passo 7 – Revise e simplifique

Ao concluir o documento, faça uma revisão crítica.

Pergunte:

> **Este documento ajuda alguém a entender rapidamente como a solução está organizada?**

Caso uma informação não contribua para responder essa pergunta, considere removê-la.

Lembre-se de que o objetivo do Architecture Overview é fornecer uma visão geral da solução, não detalhar sua implementação.

---

# Boas Práticas

* Organize a arquitetura por domínios.
* Utilize diagramas simples.
* Defina responsabilidades claras.
* Registre apenas princípios relevantes.
* Mantenha o documento pequeno.
* Deixe decisões detalhadas para os ADRs.

---

# Erros Comuns

* Misturar arquitetura com implementação.
* Organizar o sistema por camadas técnicas.
* Repetir informações do PRD.
* Descrever tecnologias antes de compreender o domínio.
* Criar diagramas excessivamente complexos.
* Tentar documentar toda a arquitetura em um único documento.

---

# Resultado Esperado

Ao concluir o Architecture Overview, qualquer integrante da equipe deverá compreender:

* Como a solução está organizada.
* Quais são os principais domínios.
* Como esses domínios se relacionam.
* Quais princípios orientam a arquitetura.
* Quais conceitos são compartilhados entre os domínios.

Os detalhes de implementação e as decisões arquiteturais específicas deverão ser registrados em documentos próprios, como os Architecture Decision Records (ADRs).

O próximo passo será registrar as principais decisões arquiteturais do projeto por meio dos ADRs, justificando as alternativas consideradas e as razões que levaram à escolha da solução adotada.
