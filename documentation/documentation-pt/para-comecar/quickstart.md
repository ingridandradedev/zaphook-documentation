---
icon: bolt
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
    - /broken/spaces/yE16Xb3IemPxJWydtPOj/pages/7FvWQMF0kTK7HGhlQfmo
---

# Quick start: como fazer sua primeira conexão

Este guia mostra **passo a passo** como configurar sua primeira integração com o **ZapHook**, utilizando o **modelo recomendado de canal de coexistência**. Ao final do processo, você terá:

* Um número de WhatsApp conectado ao ZapHook
* Eventos do WhatsApp sendo recebidos e processados
* Mensagens e eventos entregues em um endpoint HTTP de sua escolha

O modelo de coexistência é ideal para a maioria dos cenários, pois **simplifica o setup**, reduz dependências do ambiente da Meta e ainda permite escalar a integração com segurança.

***

### Pré-requisitos

Antes de iniciar, certifique-se de que você possui:

* Uma **conta ativa no ZapHook**
* Um **número de WhatsApp válido**
* Um **endpoint HTTP público** capaz de receber requisições `POST`\
  Exemplos:
  * `https://webhook.site`
  * API própria (Node, Python, FastAPI, etc.)

{% hint style="info" %}
Importante: o endpoint **precisa estar acessível publicamente**, pois o ZapHook irá enviar os eventos diretamente para ele.
{% endhint %}

***

### Etapa 1: Criar um canal no ZapHook

O **canal** representa a unidade lógica que conecta:

* um número de WhatsApp
* regras de processamento
* destinos (webhooks)

<figure><img src="../.gitbook/assets/image (22).png" alt=""><figcaption></figcaption></figure>

#### Passo a passo

1. Acesse o **painel do ZapHook**
2. Vá até a aba **Channels**
3. Clique em **Criar novo canal**
4. Selecione o tipo **Coexistência**
5. Defina um nome descritivo para o canal\
   Exemplo:\
   `WhatsApp Produção` ou `WhatsApp Atendimento`

Ao criar o canal:

* Ele ainda **não estará ativo**
* Nenhum evento será enviado até que um número e um destino sejam configurados

**Resultado esperado**: um canal criado e visível na lista de Cansi , com status inicial de configuração.

***

### Etapa 2: Conectar seu número de WhatsApp

<figure><img src="../.gitbook/assets/image (23).png" alt=""><figcaption></figcaption></figure>

Após criar o canal, o próximo passo é **conectar um número de WhatsApp** a ele.

Essa conexão é feita por meio de um **fluxo de onboarding guiado**, onde você autoriza o ZapHook a operar com sua conta da **Meta (WhatsApp Cloud API)**.

#### Como funciona o onboarding

Durante o processo, você irá:

* Autenticar-se com sua conta da Meta
* Selecionar o Business Manager correto
* Autorizar o uso do número pelo ZapHook

O ZapHook cuida automaticamente de:

* Webhooks da Meta
* Tokens
* Assinaturas de eventos

{% hint style="info" %}
&#x20;Para instruções detalhadas, consulte o[ guia completo de conexão](../how-to/gerar-token-permanente.md)
{% endhint %}

**Resultado esperado**:\
\
O canal passa a exibir:

* Um número de WhatsApp associado
* Status indicando que o número está **conectado/ativo**

***

### Etapa 3: Criar um destino (Webhook)

<figure><img src="../.gitbook/assets/image (24).png" alt=""><figcaption></figcaption></figure>

Com o canal ativo e o número conectado, é hora de definir **para onde os eventos serão enviados**.

No ZapHook, isso é feito através de um **Destination**, que representa um endpoint de destino.

#### Passo a passo

1. Acesse o **canal criado**
2. Vá até a seção **Destinations**
3. Clique em **Create Destination**

#### Configuração do destino

Preencha os campos:

* **Name**\
  Um nome identificador\
  Exemplo: `Meu Webhook`, `API Backend`, `Webhook Dev`
* **Endpoint URL**\
  A URL da sua API que receberá os eventos\
  Exemplo: [https://webhook.site/xxxxx](https://webhook.site/xxxxx)
* **HTTP Method**\
  Selecione `POST`

Esse endpoint passará a receber:

* Mensagens enviadas ao número
* Eventos de status
* Outros eventos processados pelo ZapHook

**Resultado esperado**:\
Um destino ativo, vinculado ao canal.

***

### Etapa 4: Enviar uma mensagem de teste

Com tudo configurado, é hora de testar.

1. Pegue qualquer celular
2. Envie uma **mensagem de texto simples** para o número de WhatsApp conectado\
   Exemplo: Olá, teste de integração

#### O que acontece internamente

1. A **Meta** envia o evento para o ZapHook
2. O ZapHook:
   * Recebe o evento bruto
   * Normaliza os dados
   * Aplica regras internas
3. O evento processado é encaminhado ao **destino configurado**

**Resultado esperado**:\
Uma requisição `POST` no seu endpoint contendo o payload do evento.

***

### Etapa 5: Validar o recebimento dos eventos

Você pode validar a integração de duas formas complementares.

***

#### Validação via Endpoint

No seu endpoint (ex: webhook.site ou logs da API), verifique:

* Se a requisição foi recebida
* Se o payload contém os dados esperados
* Se o retorno HTTP foi `2xx`

> 💡 Qualquer resposta fora da faixa `200–299` é considerada falha e pode gerar retry.

***

#### Validação via Dashboard do ZapHook

<figure><img src="../.gitbook/assets/image (25).png" alt=""><figcaption></figcaption></figure>

1. Acesse a aba **Monitoring**
2. Localize o evento recém-recebido

Você verá informações como:

* Timestamp
* Tipo de evento
* Destino utilizado
* Status de entrega

**Status mais comuns**

* **delivered** → evento entregue com sucesso
* **failed** → erro na entrega

***

### Etapa 6: Debug básico (em caso de falha)

Se um evento aparecer como `failed`, o próprio ZapHook fornece informações detalhadas para diagnóstico.

Você pode inspecionar:

* **Código da resposta**\
  Código HTTP retornado pelo seu endpoint
* **Corpo da resposta**\
  Conteúdo da resposta (útil para mensagens de erro)
* **URL do destino**\
  Confirme se está correta e ativa
* **Acessibilidade do servidor**\
  Verifique firewall, SSL, DNS, etc.

Além disso, o painel de eventos permite visualizar:

* Payload original (recebido da Meta)
* Payload processado (enviado ao seu sistema)
* Histórico de tentativas e retries

***

### Próximos passos

Após validar sua primeira integração, você pode evoluir gradualmente:

* Configurar **mapeamento de campos**
* Ajustar **retry**, **timeout** e políticas de entrega
* Criar **múltiplos destinos**
* Utilizar a **API de events** para monitoramento programático

***

### Observação importante

Se você precisa de **controle total da infraestrutura da Meta**\
(apps, webhooks próprios, tokens, ambientes separados):

Consulte o [**guia de setup manual**](../gerar-token-permanente.md)

Para a maioria dos casos, o **modelo de coexistência** oferece o melhor equilíbrio entre:

* simplicidade
* rapidez
* escalabilidade
