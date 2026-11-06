---
description: >-
  Passo a passo para criar um endpoint próprio, protegê-lo com X-API-Key e
  cadastrá-lo como destino no ZapHook para receber mensagens do WhatsApp em
  tempo real.
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

# Como criar uma API para receber eventos do WhatsApp no ZapHook

<figure><img src="../.gitbook/assets/image (29).png" alt=""><figcaption></figcaption></figure>

O ZapHook recebe os eventos do WhatsApp (mensagens, áudios, mídias, status de entrega) e os **encaminha para a sua API**. Cada API que recebe esses eventos é chamada de **Destino**.

Neste guia você vai:

1. Criar um endpoint `POST` no seu sistema
2. Definir o formato do corpo (body) que ele vai receber
3. Proteger o endpoint com uma chave (`X-API-Key`)
4. Cadastrar o endpoint em **Destinos > Criar destino**
5. Testar a integração

> **Pré-requisito:** ter ao menos um canal do WhatsApp conectado no ZapHook.

***

### Como funciona

```
WhatsApp  ──►  ZapHook (Canal)  ──►  Destino (sua API)  ──►  seu CRM / sistema
```

Quando chega uma mensagem no seu canal, o ZapHook monta um JSON com os dados que você escolheu e faz um `POST` na URL do seu destino, enviando junto os headers que você configurou (por exemplo, a sua chave de autenticação).

***

### Passo 1 — Crie um endpoint POST

Sua API precisa de uma rota pública, acessível pela internet por **HTTPS**, que aceite o método `POST` com corpo em JSON. Por exemplo:

```
POST https://api.suaempresa.com/webhooks/whatsapp
```

Essa rota deve:

* ler o JSON do corpo da requisição;
* salvar ou processar os dados;
* responder rápido com status `200` (ou qualquer `2xx`).

> **Dica:** não faça processamento pesado dentro da requisição. Receba, grave numa fila ou no banco e responda `200` o quanto antes. Respostas lentas ou com erro (`4xx`/`5xx`) contam como falha de entrega.

***

### Passo 2 — Defina o body que sua API vai receber

Você decide quais informações o seu sistema precisa. Os casos mais comuns:

| Informação                        | Para que serve                       |
| --------------------------------- | ------------------------------------ |
| Número do WhatsApp de quem enviou | identificar o contato                |
| Nome do contato                   | exibir no CRM                        |
| Conteúdo da mensagem              | texto digitado pelo cliente          |
| ID da mensagem (wamid)            | evitar duplicidade                   |
| Tipo da mensagem                  | texto, áudio, imagem, documento etc. |
| Mídia (URL, tipo de arquivo)      | baixar imagens, áudios e documentos  |
| Data e hora                       | ordenar os eventos                   |

Um exemplo de body que o seu endpoint poderia receber:

```json
{
  "evento_id": "e3b0c442-98fc-4a1b-9d6e-0a1f2b3c4d5e",
  "tipo_evento": "message",
  "telefone": "5511999999999",
  "nome": "Maria Silva",
  "mensagem": "Olá, gostaria de saber mais sobre o apartamento",
  "id_mensagem": "wamid.HBgNNTUxMTk5OTk5OTk5ORUCABIYFjNFQjA",
  "midia_url": null,
  "data_hora": "2026-11-05T14:32:10Z"
}
```

Esse formato é só um exemplo. **Os nomes dos campos são seus**: no próximo passo, você diz ao ZapHook qual dado do WhatsApp vai em cada campo.

***

### Passo 3 — Proteja o endpoint com X-API-Key

Como a rota é pública, qualquer pessoa que descubra a URL poderia enviar dados falsos para ela. A forma mais simples de evitar isso é exigir uma **chave secreta** em um header, normalmente `X-API-Key`.

1. Gere uma chave longa e aleatória (por exemplo, 32 bytes em hexadecimal).
2. Guarde a chave numa variável de ambiente do seu servidor.
3. Em cada requisição, compare o header `X-API-Key` com a chave guardada.
4. Se não bater, responda `401` e **não processe nada**.

Para gerar uma chave:

```bash
openssl rand -hex 32
```

#### Exemplo em Node.js (Express)

```js
import express from "express";
import crypto from "node:crypto";

const app = express();
app.use(express.json());

const API_KEY = process.env.ZAPHOOK_API_KEY;

function chaveValida(recebida) {
  if (!recebida || !API_KEY) return false;
  const a = Buffer.from(recebida);
  const b = Buffer.from(API_KEY);
  return a.length === b.length && crypto.timingSafeEqual(a, b);
}

app.post("/webhooks/whatsapp", (req, res) => {
  if (!chaveValida(req.header("X-API-Key"))) {
    return res.status(401).json({ erro: "Não autorizado" });
  }

  const { evento_id, telefone, nome, mensagem, id_mensagem } = req.body;
  console.log("Evento recebido:", evento_id, telefone, mensagem);

  // TODO: salvar no banco / enviar para uma fila

  return res.status(200).json({ ok: true });
});

app.listen(3000);
```

#### Exemplo em Python (FastAPI)

```python
import hmac
import os
from fastapi import FastAPI, Header, HTTPException, Request

app = FastAPI()
API_KEY = os.environ["ZAPHOOK_API_KEY"]

@app.post("/webhooks/whatsapp")
async def receber(request: Request, x_api_key: str | None = Header(default=None)):
    if not x_api_key or not hmac.compare_digest(x_api_key, API_KEY):
        raise HTTPException(status_code=401, detail="Não autorizado")

    dados = await request.json()
    print("Evento recebido:", dados.get("evento_id"), dados.get("telefone"))

    # TODO: salvar no banco / enviar para uma fila

    return {"ok": True}
```

> **Boas práticas de segurança**
>
> * Use sempre **HTTPS**.
> * Nunca coloque a chave no código ou no repositório; use variável de ambiente.
> * Compare a chave com função de tempo constante (como nos exemplos acima).
> * Troque a chave periodicamente e sempre que suspeitar de vazamento.

***

### Passo 4 — Cadastre a API no ZapHook

Com a API no ar, abra o ZapHook em **Destinos** e clique em **Criar Destino**. O cadastro tem quatro abas: **Básico**, **Headers**, **Mapeamento de campos** e **Avançado**.

#### Aba Básico

Preencha:

* **Canal:** o canal do WhatsApp cujos eventos você quer encaminhar.
* **Nome:** um nome para identificar o destino (ex.: `CRM Imobiliária`).
* **Descrição:** opcional, para lembrar a finalidade.
* **Endpoint URL:** a URL do seu endpoint (ex.: `https://api.suaempresa.com/webhooks/whatsapp`).
* **Método HTTP:** `POST`.
* **Ativo:** deixe ligado para começar a receber eventos.

#### Aba Headers

Aqui você informa os headers enviados em **cada requisição**. É onde entra a sua chave de autenticação:

1. Clique em **Adicionar Header**.
2. Nome do header: `X-API-Key`
3. Valor: a chave que você gerou no Passo 3.

Se o seu endpoint também exigir `Content-Type: application/json`, adicione-o do mesmo jeito.

#### Aba Mapeamento de campos

Essa é a aba que define **o body que sua API vai receber**. No editor **JSON Field Mapping** você escreve o JSON no formato que o seu sistema espera e, no lugar de cada valor, usa um **placeholder** do ZapHook. O ZapHook troca cada placeholder pelo dado real do evento antes de enviar.

Do lado direito fica a lista de **Placeholders Disponíveis**, organizada por categoria (Comum, Mensagem etc.). Você pode **arrastar** um placeholder para o editor ou **clicar** nele para inserir na posição do cursor. O editor mostra **Valid JSON** quando o JSON está correto.

Alguns placeholders úteis:

| Placeholder                                       | O que traz                              |
| ------------------------------------------------- | --------------------------------------- |
| `{{event_id}}`                                    | ID do evento                            |
| `{{channel_id}}`                                  | ID do canal                             |
| `{{event_category}}`                              | categoria do evento                     |
| `{{event_type}}`                                  | tipo do evento                          |
| `{{timestamp_iso}}`                               | data e hora (ISO)                       |
| `{{data.sender_phone}}`                           | telefone de quem enviou                 |
| `{{data.sender_name}}`                            | nome do contato                         |
| `{{data.text_body}}`                              | texto da mensagem                       |
| `{{data.message_wamid}}`                          | ID da mensagem no WhatsApp              |
| `{{data.media.url}}`                              | URL da mídia (imagem, áudio, documento) |
| `{{data.media.mime_type}}`                        | tipo do arquivo da mídia                |
| `{{data.media.caption}}`                          | legenda da mídia                        |
| `{{data.media.transcription}}`                    | transcrição de áudio                    |
| `{{data.business_metadata.display_phone_number}}` | número do seu WhatsApp Business         |

> A lista completa e atualizada está sempre na própria tela. Confira lá os placeholders disponíveis para o seu caso.

**Exemplo:** mapeando o body do Passo 2.

```json
{
  "evento_id": "{{event_id}}",
  "tipo_evento": "{{event_type}}",
  "telefone": "{{data.sender_phone}}",
  "nome": "{{data.sender_name}}",
  "mensagem": "{{data.text_body}}",
  "id_mensagem": "{{data.message_wamid}}",
  "midia_url": "{{data.media.url}}",
  "data_hora": "{{timestamp_iso}}"
}
```

Os nomes à esquerda (`telefone`, `mensagem`…) são os que a **sua API** espera. Os valores à direita são os placeholders do ZapHook.

#### Aba Avançado

Nesta aba ficam as configurações extras do destino, como tempo limite da requisição e opções de reenvio. Para a maioria dos casos, os valores padrão funcionam bem. Também há a opção de enviar o **payload original do WhatsApp** sem mapeamento, caso você prefira receber o JSON completo e fazer o tratamento no seu sistema.

#### Finalizar

Clique em **Criar Destino**. O destino aparece na lista com a etiqueta **Ativo**.

***

### Passo 5 — Teste a integração

1. Envie uma mensagem para o número do WhatsApp conectado ao canal.
2. Confira nos logs da sua API se o `POST` chegou com o body esperado.
3. No ZapHook, abra **Monitoramento** para ver o status de entrega de cada evento (entregue, falhou, pendente).

#### Problemas comuns

| Sintoma                                      | Causa provável                                        | Como resolver                                                      |
| -------------------------------------------- | ----------------------------------------------------- | ------------------------------------------------------------------ |
| Evento aparece como falha com `401`          | `X-API-Key` ausente ou diferente da chave do servidor | Revise o header na aba **Headers** e a variável de ambiente da API |
| Evento aparece como falha com `404`          | URL do endpoint errada                                | Confira a **Endpoint URL** e a rota da API                         |
| Evento aparece como falha com `5xx`          | Erro interno na sua API                               | Verifique os logs do servidor                                      |
| Falha por tempo esgotado                     | Sua API demora para responder                         | Responda `200` rápido e processe em segundo plano                  |
| Campos chegam vazios ou como texto `{{...}}` | Placeholder digitado incorretamente                   | Insira os placeholders clicando na lista da direita                |
| Nada chega                                   | Destino inativo ou canal incorreto                    | Confirme que o destino está **Ativo** e vinculado ao canal certo   |

***

### Dicas finais

* **Evite duplicidade.** Em caso de falha, o mesmo evento pode ser reenviado. Use `{{event_id}}` ou `{{data.message_wamid}}` como identificador único e ignore o que já foi processado.
* **Mídias:** a URL em `{{data.media.url}}` é o link do arquivo. Baixe e armazene o que for importante para você.
* **Responda sempre com `2xx`** quando o evento foi recebido, mesmo que o processamento aconteça depois.
* **Um destino por finalidade.** Você pode ter vários destinos (por exemplo, um para o CRM e outro para um sistema de relatórios).

Pronto! Sua API já está recebendo os eventos do WhatsApp através do ZapHook. 🚀

Ficou com dúvida? Fale com a gente pelo suporte do ZapHook.
