---
description: >-
  Entenda como os canais conectam o WhatsApp ao ZapHook e permitem receber,
  processar e integrar eventos em tempo real
icon: plug-circle-plus
layout:
  width: default
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
  metadata:
    visible: true
  tags:
    visible: true
  actions:
    visible: true
  anchors:
    visible: true
metaLinks:
  alternates:
    - /broken/spaces/yE16Xb3IemPxJWydtPOj/pages/lWdZgI2P4YIODQ7uazsl
---

# Canais

### Visão geral

Um **canal** é a forma como o ZapHook conecta um número do **WhatsApp Business (Cloud API)** ao seu sistema.

Quando um canal está configurado, o ZapHook passa a ser responsável por receber todos os eventos enviados pela Meta — como mensagens, status e mídias —, processar esses dados e entregá-los já estruturados para o seu backend.

Na prática, você pode pensar em um canal como um **ponto de integração**: ele liga o WhatsApp de um cliente (ou da sua empresa) à sua aplicação, sem que você precise lidar diretamente com a complexidade da infraestrutura da Meta.

***

### Tipos de canais

O ZapHook oferece duas formas de configurar essa conexão: **Coexistência** e **Setup manual**.\
A principal diferença entre elas está no nível de complexidade e na forma como a integração com a Meta é feita.

***

### Coexistência (recomendado)

O modo de **coexistência** é a forma mais simples e moderna de integrar com o WhatsApp usando o ZapHook.

Nesse modelo, você não precisa criar aplicativos na Meta Developers nem configurar manualmente webhooks, tokens ou permissões complexas. Basta ter um número de WhatsApp vinculado a um **Business Manager**, e a conexão já pode ser estabelecida.

Isso torna o processo muito mais rápido e acessível, especialmente para produtos SaaS que precisam permitir que múltiplos clientes conectem seus próprios números.

Além disso, como o próprio nome sugere, esse modo permite que o ZapHook funcione junto com outras integrações existentes, sem exigir que você desmonte sua infraestrutura atual.

Esse é o modelo ideal quando o objetivo é:

* reduzir o tempo de setup
* simplificar o onboarding de clientes
* evitar dependência de configuração manual na Meta
* escalar integrações com múltiplos números

Na maioria dos casos, esse será o melhor ponto de partida.

***

### Setup manual

O modo de **setup manual** segue o fluxo tradicional da integração com a Meta Cloud API.

Nesse caso, você precisa criar e configurar um aplicativo no ambiente de desenvolvedores da Meta, definir manualmente o webhook e gerenciar itens como tokens de acesso, validação de assinatura e permissões.

O ZapHook continua sendo responsável por processar e encaminhar os eventos, mas a etapa de configuração inicial depende diretamente de você.

Esse modelo oferece mais controle sobre a integração, porém exige maior conhecimento técnico e mais tempo de setup.

Ele é mais indicado quando:

* você já possui experiência com a API da Meta
* precisa de uma configuração mais personalizada
* já existe uma infraestrutura própria em funcionamento

***

### Qual usar?

Se você está começando ou quer a forma mais rápida de integrar, use **Coexistência**.

Se você precisa de controle total sobre a configuração da Meta ou já tem uma integração existente mais complexa, o **Setup manual** pode fazer mais sentido.

***

### Observações

Cada canal possui sua própria configuração e funciona de forma independente dentro do ZapHook.

Isso inclui:

* uma URL de webhook exclusiva
* parâmetros de autenticação
* controle de ativação ou desativação

Além disso, um canal pode ser vinculado a um `phone_number_id` ou a um `waba_id`.\
Quando esses dados não são definidos, o canal pode aceitar eventos de múltiplas origens associadas.
