---
icon: globe-pointer
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
    - /broken/spaces/yE16Xb3IemPxJWydtPOj/pages/QPzbTvC6XsT5gERiU43E
---

# Visão geral

O **ZapHook** é uma plataforma de **Webhook as a Service** criada para simplificar a integração entre a **API Oficial do WhatsApp Business (Cloud API)** e os sistemas da sua empresa.

Em vez de lidar diretamente com webhooks complexos, validações de segurança, respostas em tempo crítico e riscos de perda de mensagens, o ZapHook atua como uma **infraestrutura intermediária confiável**, responsável por receber os eventos do WhatsApp, processá‑los e entregá‑los de forma segura ao seu sistema.

Na prática, você continua usando a API oficial da Meta, mas sem precisar construir e manter toda a infraestrutura que ela exige.

***

### O problema que o ZapHook resolve

Ao integrar diretamente com a Cloud API do WhatsApp, desenvolvedores e equipes técnicas normalmente precisam lidar com:

* Webhooks que exigem **servidores sempre online**
* **Validação de assinaturas** e controle de segurança manual
* Payloads **complexos e difíceis de consumir**
* Risco de **perda de mensagens** se o servidor ficar indisponível por poucos segundos

O ZapHook elimina essa complexidade ao assumir essa camada crítica da integração.

***

### O que o ZapHook faz

De forma resumida, o ZapHook:

* Recebe os eventos do WhatsApp de forma imediata
* Processa e organiza os dados recebidos
* Garante a entrega dos eventos ao seu sistema
* Permite que os dados sejam enviados para um ou vários destinos

Tudo isso com observabilidade, logs e controle centralizado.

***

### Como o ZapHook se encaixa na arquitetura

O ZapHook funciona como um **middleware de eventos**, posicionado entre:

* **Origem:** WhatsApp Business Cloud API (Meta)
* **Destino:** Seu backend, CRM, banco de dados ou ferramenta de automação

A Meta envia os eventos para o ZapHook, e o ZapHook cuida do restante do fluxo.

***

### Próximos passos

Se quiser se aprofundar, recomendamos seguir a documentação na ordem:

* [**Canais (Channels):**](../conceitos/canais-e-destinos/) como configurar a entrada de eventos do WhatsApp
* [**Destinos (Destinations)**](../conceitos/destinos.md)**:** como enviar os dados para seu sistema
* [**Mapping e Transformações**](../conceitos/destinos.md)**:** como ajustar o formato do payload
* [**Monitoramento e Logs:**](../conceitos/monitoramento-e-logs.md) como acompanhar entregas e falhas

Cada um desses tópicos é explicado em detalhes nos próximos capítulos.
