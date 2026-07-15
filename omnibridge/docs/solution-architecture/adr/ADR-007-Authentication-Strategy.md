# ADR-007 – Authentication Strategy

**Produto:** OmniBridge
**Versão:** 1.0
**Status:** Accepted
**Tipo:** Tático

---

## Contexto

O OmniBridge participa de dois fluxos distintos de autenticação.

No primeiro, usuários e sistemas consumidores acessam as funcionalidades e APIs disponibilizadas pela plataforma.

No segundo, o OmniBridge autentica-se perante marketplaces e outros sistemas externos para consumir suas APIs.

Esses fluxos possuem responsabilidades, credenciais e mecanismos diferentes.

A estratégia de autenticação deve preservar os limites dos módulos, proteger informações sensíveis e impedir que mecanismos técnicos de segurança sejam propagados para o domínio de negócio.

---

## Questão Arquitetural

Como organizar a autenticação dos usuários e sistemas consumidores do OmniBridge e, ao mesmo tempo, tratar os diferentes mecanismos de autenticação exigidos pelos sistemas externos?

---

## Alternativas Consideradas

### Centralizar toda a autenticação no módulo Identity

O módulo Identity seria responsável tanto pela autenticação dos usuários do OmniBridge quanto pelas credenciais utilizadas nas integrações externas.

**Vantagens**

* Centralização dos mecanismos de autenticação.
* Ponto único para gerenciamento de credenciais.
* Aparente simplificação da solução.

**Desvantagens**

* Concentração de responsabilidades distintas.
* Acoplamento entre Identity e os detalhes dos marketplaces.
* Necessidade de o módulo Identity conhecer OAuth, API Keys e outros mecanismos específicos de integrações.
* Redução da autonomia do módulo Integration.

---

### Tratar toda autenticação nos adaptadores

Cada adaptador seria responsável por autenticar usuários, consumidores e sistemas externos relacionados ao seu contexto.

**Vantagens**

* Responsabilidade próxima da integração.
* Flexibilidade para mecanismos específicos.

**Desvantagens**

* Duplicação de lógica de autenticação dos usuários.
* Ausência de uma política central para acesso ao OmniBridge.
* Dificuldade para gerenciar perfis, permissões e autorização.
* Mistura entre segurança da plataforma e segurança das integrações.

---

### Separar autenticação da plataforma e autenticação das integrações

O módulo Identity gerencia a autenticação e autorização de usuários e sistemas consumidores do OmniBridge.

O módulo Integration gerencia a autenticação necessária para comunicação com marketplaces e demais sistemas externos.

Cada adaptador conhece os detalhes técnicos exigidos pelo sistema com o qual se comunica.

**Vantagens**

* Responsabilidades claramente separadas.
* Maior aderência aos limites dos domínios.
* Identity permanece independente dos marketplaces.
* Adaptadores permanecem responsáveis pelas particularidades externas.
* Facilita a evolução dos mecanismos de autenticação.
* Evita propagação de detalhes técnicos para o domínio.

**Desvantagens**

* Existem dois contextos de segurança a serem administrados.
* Exige políticas distintas para credenciais internas e externas.
* Requer disciplina para não duplicar responsabilidades.

---

## Decisão

O OmniBridge adotará estratégias separadas para autenticação de entrada e autenticação de saída.

O módulo **Identity** será responsável pela autenticação e autorização dos usuários e sistemas consumidores que acessarem o OmniBridge.

O módulo **Integration** será responsável por coordenar a autenticação utilizada na comunicação com marketplaces e outros sistemas externos.

Cada adaptador será responsável por implementar os detalhes do mecanismo de autenticação exigido pelo sistema externo correspondente.

---

## Autenticação de Entrada

O acesso às APIs e funcionalidades do OmniBridge será protegido pelo módulo Identity.

Para o MVP, serão utilizados:

* Spring Security.
* Tokens JWT.
* Autenticação baseada em usuário e credenciais.
* Autorização por perfis e permissões.

Fluxo conceitual:

```text
Usuário ou sistema consumidor
            |
            v
      Autenticação
            |
            v
           JWT
            |
            v
     Identity Module
            |
            v
       OmniBridge API
```

A validação dos tokens ocorrerá antes da execução dos casos de uso da aplicação.

O domínio não conhecerá JWT, sessões, filtros de segurança ou mecanismos de autenticação.

---

## Autenticação com Sistemas Externos

A autenticação perante marketplaces e outros sistemas externos será responsabilidade do módulo Integration e de seus adaptadores.

Cada adaptador poderá utilizar o mecanismo exigido pelo sistema correspondente, como:

* OAuth 2.0.
* API Key.
* Basic Authentication.
* Assinatura de requisições.
* Certificados ou mTLS.

Fluxo conceitual:

```text
Integration Module
        |
        v
Marketplace Adapter
        |
        v
Authentication Client
        |
        v
External System
```

Os detalhes específicos de autenticação permanecerão restritos ao adaptador e à sua infraestrutura.

---

## Credenciais e Tokens

Credenciais, chaves, tokens e certificados serão tratados como **segredos**.

Essas informações:

* Não poderão ser armazenadas diretamente no código-fonte.
* Não poderão ser expostas em logs.
* Não poderão ser incluídas em eventos ou DTOs públicos.
* Deverão ser protegidas durante armazenamento e transmissão.
* Deverão possuir acesso restrito ao módulo responsável.

Refresh tokens e demais credenciais persistentes deverão ser armazenados de forma protegida.

A tecnologia definitiva de gerenciamento de segredos será escolhida durante a definição da infraestrutura da solução.

---

## Renovação de Credenciais

Quando o sistema externo utilizar credenciais temporárias, o adaptador correspondente será responsável por:

* Identificar a expiração.
* Renovar as credenciais.
* Atualizar o segredo armazenado.
* Tratar falhas de renovação.
* Impedir o uso de credenciais inválidas.
* Informar ao módulo de Monitoramento quando houver necessidade de intervenção.

A lógica de renovação não deverá ser propagada para os domínios de Catálogo, Pedidos ou Monitoramento.

---

## Autorização

O módulo Identity também será responsável por determinar quais funcionalidades um usuário ou sistema consumidor poderá acessar.

A autorização deverá ocorrer antes da execução dos casos de uso.

As regras de negócio continuarão sendo responsabilidade dos respectivos domínios.

A autorização técnica não deverá substituir validações de negócio.

---

## Regras Arquiteturais

* A autenticação da API do OmniBridge pertence ao módulo Identity.
* A autenticação perante sistemas externos pertence ao módulo Integration.
* Cada adaptador conhece o mecanismo de autenticação exigido pelo sistema externo correspondente.
* O domínio não dependerá de JWT, OAuth, Spring Security ou outros mecanismos técnicos.
* Credenciais serão tratadas como segredos.
* Tokens e credenciais não poderão ser expostos em logs, eventos ou APIs públicas.
* Somente o módulo proprietário poderá acessar suas credenciais.
* Falhas de autenticação e renovação deverão ser rastreáveis.
* Mecanismos de segurança permanecerão encapsulados nas camadas `api` e `infrastructure`.

---

## Justificativa

A autenticação dos consumidores do OmniBridge e a autenticação perante sistemas externos representam problemas diferentes.

Centralizar ambas no módulo Identity faria com que esse domínio passasse a conhecer detalhes específicos das integrações, comprometendo a separação de responsabilidades.

Ao dividir essas funções, o módulo Identity permanece responsável pela segurança da plataforma, enquanto o módulo Integration e seus adaptadores permanecem responsáveis pelas particularidades dos sistemas externos.

Essa abordagem preserva os limites dos domínios, reduz o acoplamento e permite que cada mecanismo evolua independentemente.

---

## Consequências

### Positivas

* Separação clara entre segurança da plataforma e segurança das integrações.
* Identity permanece independente dos marketplaces.
* Adaptadores encapsulam mecanismos externos.
* Domínios de negócio permanecem livres de dependências de segurança.
* Facilidade para suportar diferentes mecanismos de autenticação.
* Maior controle sobre credenciais e tokens.

### Negativas

* Dois contextos distintos de segurança precisam ser administrados.
* Renovação e proteção de credenciais exigem implementação específica por integração.
* A solução dependerá de uma estratégia segura de gerenciamento de segredos.
* Falhas de autenticação externa poderão interromper temporariamente sincronizações.

---

## Critérios para Revisão

Esta decisão deverá ser revisada caso ocorra uma ou mais das seguintes situações:

* A adoção de um provedor externo de identidade altere as responsabilidades do módulo Identity.
* Novos consumidores exijam autenticação máquina a máquina ou federação de identidade.
* O crescimento das integrações torne necessária uma plataforma centralizada para gerenciamento de credenciais externas.
* Novos requisitos regulatórios ou de segurança exijam mecanismos adicionais.
* A evolução para uma arquitetura distribuída altere a validação e propagação de identidade entre serviços.
* Os mecanismos atuais deixem de atender aos requisitos de segurança, auditoria ou disponibilidade.

Até que um desses cenários se concretize, a autenticação da plataforma e a autenticação das integrações permanecerão separadas conforme estabelecido neste ADR.
