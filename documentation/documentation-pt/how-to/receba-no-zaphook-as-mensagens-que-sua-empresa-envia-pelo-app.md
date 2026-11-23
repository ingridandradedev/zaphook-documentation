---
description: >-
  Receba as mensagens enviadas pelo app do WhatsApp Business no ZapHook: como
  liberar o evento, formato do payload e placeholders.
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
---

# Como processar eventos de mensagens enviadas pelo app do WhatsApp Business

Em um canal de **coexistência**, sua empresa usa o WhatsApp Business no celular e a API oficial ao mesmo tempo, no mesmo número. O ZapHook registra as mensagens dos seus clientes, os status das mensagens enviadas pela API e também as mensagens que a sua equipe envia pelo app.

Esse último tipo de evento se chama **`smb_message_echoes`** (eco de mensagem enviada pelo app).

***

### O que é e quando acontece

Quando você, ou alguém da sua equipe, envia uma mensagem pelo **app WhatsApp Business** (ou por um dispositivo vinculado a ele), a Meta avisa o ZapHook. O ZapHook então:

1. registra o evento no **Monitoramento**;
2. mostra a mensagem na **Caixa de entrada**, como mensagem enviada;
3. envia o evento aos **destinos** que estiverem configurados para recebê-lo.

O evento existe somente em canais de **coexistência**. Mensagens enviadas **pela API** não geram esse evento. Para elas, você recebe os status (`sent`, `delivered`, `read`, `failed`).

***

### Como habilitar

#### 1. Canal

Não é preciso configurar nada na Meta. Basta ter um canal de coexistência ativo, com o app WhatsApp Business conectado ao número.

#### 2. Destino

Cada destino decide, pelo **Filtro de eventos**, quais eventos recebe. Sem filtro, o destino recebe tudo, inclusive as mensagens enviadas pelo app.

<figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

Para escolher o que o destino recebe:

1. Vá em **Destinos** e abra o destino desejado.
2. Clique em **Editar** e abra a aba **Avançado**.
3. Na seção **Filtro de eventos**, escolha uma das opções:
   * **Receber todos os eventos**: o destino recebe tudo, inclusive as mensagens enviadas pelo celular.
   * **Receber todos, exceto os selecionados**: o item **"Mensagens enviadas pelo celular"** (`smb_message_echoes`) fica **marcado** quando você quer bloquear o evento, e **desmarcado** quando quer receber.
   * **Receber somente os selecionados**: marque **"Mensagens enviadas pelo celular"** junto com os outros tipos que você quiser.
4. Salve.

#### Pela API

O filtro é o campo `event_filter` do destino:

```http
PUT /api/v1/coexistence/destinations/{destination_id}
```

```json
{ "event_filter": {} }
```

* `{}` faz o destino receber tudo.
* `{"exclude_types": ["smb_message_echoes"]}` bloqueia só esse evento.
* `{"event_types": ["text", "smb_message_echoes"]}` recebe somente esses tipos.

A lista oficial de valores aceitos está em `GET /api/v1/coexistence/event-types`.

***

### Como o evento chega

No Monitoramento e no `event_filter`, o evento aparece como `smb_message_echoes`. Dentro do payload normalizado, ele chega assim:

| Campo            | Valor                                                                                                              |
| ---------------- | ------------------------------------------------------------------------------------------------------------------ |
| `event_category` | `message_echo`                                                                                                     |
| `event_type`     | o tipo real da mensagem: `text`, `image`, `audio`, `video`, `document`, `sticker`, `location`, `edit`, `revoke`... |
| `data.direction` | `outbound`                                                                                                         |

#### Quem é quem

Em um echo, **quem enviou foi a sua empresa**. Por isso o remetente é o seu número, e o cliente é o destinatário.

| Placeholder                | Mensagem recebida do cliente | Mensagem enviada pelo app (echo) |
| -------------------------- | ---------------------------- | -------------------------------- |
| `{{event_category}}`       | `message`                    | `message_echo`                   |
| `{{event_type}}`           | tipo da mensagem (`text`...) | tipo da mensagem (`text`...)     |
| `{{data.direction}}`       | `inbound`                    | `outbound`                       |
| `{{data.sender_phone}}`    | telefone do cliente          | **número da empresa**            |
| `{{data.sender_wa_id}}`    | wa\_id do cliente            | **número da empresa**            |
| `{{data.sender_name}}`     | nome do perfil do cliente    | **nome do canal no ZapHook**     |
| `{{data.recipient_phone}}` | não existe                   | **telefone do cliente**          |
| `{{data.text_body}}`       | texto                        | texto                            |
| `{{data.message_wamid}}`   | id da mensagem               | id da mensagem                   |

> **Atenção:** se o seu mapeamento usa `{{data.sender_phone}}` como "telefone do contato", nos echoes ele recebe o **número da empresa**. Para esse caso, use `{{data.recipient_phone}}`, que é o telefone do cliente.

#### Exemplo de payload normalizado

```json
{
  "event_id": "5f3a1c52-8d1e-4c43-9b8a-1f2d3e4a5b6c",
  "coexistence_channel_id": "a1b2c3d4-0000-4000-8000-000000000001",
  "event_category": "message_echo",
  "event_type": "text",
  "timestamp_iso": "2026-11-23T20:01:55Z",
  "data": {
    "message_wamid": "wamid.HBgTQlIuMTU0MTk1MzA4NDAxOTI2MRUUABEYIEE1",
    "sender_phone": "551100000000",
    "sender_wa_id": "551100000000",
    "sender_name": "Minha Empresa",
    "text_body": "Olá! Seu pedido já saiu para entrega.",
    "recipient_phone": "5511999999999",
    "direction": "outbound",
    "business_metadata": {
      "phone_number_id": "100000000000001",
      "display_phone_number": "551100000000"
    }
  }
}
```

#### Exemplo do payload original da Meta

Se o seu destino usa **payload bruto** (`raw_payload`), você recebe exatamente o que a Meta enviou:

```json
{
  "object": "whatsapp_business_account",
  "entry": [
    {
      "id": "100000000000000",
      "changes": [
        {
          "field": "smb_message_echoes",
          "value": {
            "messaging_product": "whatsapp",
            "metadata": {
              "display_phone_number": "551100000000",
              "phone_number_id": "100000000000001"
            },
            "contacts": [
              { "wa_id": "5511999999999", "user_id": "BR.1234567890123456" }
            ],
            "message_echoes": [
              {
                "id": "wamid.HBgTQlIuMTU0MTk1MzA4NDAxOTI2MRUUABEYIEE1",
                "from": "551100000000",
                "to": "5511999999999",
                "type": "text",
                "text": { "body": "Olá! Seu pedido já saiu para entrega." },
                "timestamp": "1795378030",
                "to_user_id": "BR.1234567890123456"
              }
            ]
          }
        }
      ]
    }
  ]
}
```

***

### Usando no mapeamento de campos da configuração de destino

<figure><img src="../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

Exemplo para um CRM que registra tudo o que a empresa envia:

```json
{
  "customer_phone": "{{data.recipient_phone}}",
  "message": "{{data.text_body}}",
  "sent_at": "{{timestamp_iso}}",
  "message_id": "{{data.message_wamid}}",
  "direction": "{{data.direction}}"
}
```

Para receber mensagens dos clientes e mensagens enviadas pelo app no mesmo destino, envie o campo `direction` (ou o `event_category`) ao seu sistema. Assim ele separa o que o cliente escreveu do que a empresa enviou:

```json
{
  "message": "{{data.text_body}}",
  "timestamp": "{{timestamp_iso}}",
  "message_id": "{{data.message_wamid}}",
  "phone_number": "{{data.sender_phone}}",
  "direction": "{{data.direction}}",
  "event_category": "{{event_category}}"
}
```

Os placeholders disponíveis estão em `GET /api/v1/placeholders?event_category=message_echo`.

***

### Cuidados importantes

#### Agentes de IA e automações

Se o seu destino responde automaticamente ao cliente (um chatbot, um agente de IA), **não libere esse evento nele**, ou ignore os eventos com `direction = outbound`. Como o echo traz o **seu número** como remetente, um agente que não filtre pode tratar a sua própria resposta como se fosse uma mensagem de cliente e responder a si mesmo.

#### O que o echo não traz

* **Mídia:** imagens, áudios, vídeos e documentos chegam com os metadados (id, tipo, hash, legenda). O ZapHook **não baixa nem transcreve** a mídia de mensagens enviadas pelo app.
* **Mensagens editadas e apagadas:** chegam como eventos `edit` e `revoke`. Na Caixa de entrada elas não criam uma linha nova.
* **Nome da empresa:** a Meta não envia o nome do perfil da empresa nesse evento. O `sender_name` vem do **nome do canal** no ZapHook.

#### Cota de uso

Cada eco recebido conta como um evento processado, como qualquer outro.

***

### Como conferir se está funcionando

1. No celular, envie uma mensagem a um cliente pelo app WhatsApp Business.
2. Abra o **Monitoramento**. Você deve ver um evento do tipo **`smb_message_echoes`**, do seu canal.
3. Abra a **Caixa de entrada**. A mensagem aparece na conversa do cliente, como enviada.
4. No seu sistema (ou no webhook.site), confira se o destino recebeu o payload com `event_category = message_echo`.

#### O evento aparece no Monitoramento, mas não chega ao meu sistema

O filtro do destino está bloqueando. Confira em **Destinos > Editar > Avançado > Filtro de eventos** se "Mensagens enviadas pelo celular" está liberado. Eventos bloqueados pelo filtro continuam aparecendo no Monitoramento, mas não são enviados ao destino.

#### Nada aparece no Monitoramento

* Confirme que o canal é de **coexistência** e está **ativo**.
* Confirme que a mensagem foi enviada **pelo app WhatsApp Business** (ou por um dispositivo vinculado a ele), e não pela API.
* Mensagens da API geram apenas status (`sent`, `delivered`, `read`), nunca `smb_message_echoes`.

***

### Resumo

|                             |                                                  |
| --------------------------- | ------------------------------------------------ |
| Evento                      | `smb_message_echoes`                             |
| `event_category` no payload | `message_echo`                                   |
| `event_type` no payload     | tipo real da mensagem (`text`, `image`...)       |
| Remetente (`sender_phone`)  | número da sua empresa                            |
| Cliente                     | `recipient_phone`                                |
| Controle por destino        | Destinos > Editar > Avançado > Filtro de eventos |
| Sem filtro                  | o destino recebe tudo, inclusive este evento     |
| Disponível em               | canais de coexistência                           |
