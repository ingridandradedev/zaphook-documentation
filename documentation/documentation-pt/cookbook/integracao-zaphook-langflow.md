---
icon: link
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

# Integração ZapHook + Langflow

{% embed url="https://youtu.be/8Q2OgRxL9wA?si=AsDvPkW66B6L77xA" %}

Este guia ensina como conectar o ZapHook ao Langflow para que mensagens recebidas no WhatsApp sejam processadas por seus fluxos de Inteligência Artificial.

Nosso objetivo eé receber uma mensagem no WhatsApp, enviá-la para um fluxo no Langflow (ex: GPT-4, Llama 3) e processar a intenção do usuário.

### Pré-requisitos

1. Conta ativa no ZapHook com canal de WhatsApp conectado.
2. Instância do Langflow rodando (Local ou Cloud).

{% hint style="info" %}
Nota: Se o seu Langflow estiver rodando localmente (localhost), você precisará de um túnel como o Ngrok para que o ZapHook consiga acessá-lo.
{% endhint %}

### Passo 1: Configurar o Webhook no Langflow

Primeiro, precisamos criar a "porta de entrada" no seu fluxo de IA.

1. Crie um novo fluxo o abra um existente no Langflow.

<figure><img src="../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>

1. Na barra lateral esquerda, vá em Data (ou procure na barra de busca) e arraste o componente Webhook para o canvas (área de trabalho).
2. No componente Webhook:
   * Localize o campo Endpoint.
   * Copie a URL fornecida (ex: `https://langflow-api.seu-dominio.com/api/v1/webhook/...`).
   * _Dica:_ Se houver configurações de rota, certifique-se de que o método aceito seja POST.

> Importante: O componente Webhook do Langflow serve para injetar dados no início do fluxo. Ele disponibilizará o JSON que o ZapHook enviar através da saída "Data".

***

### Passo 2: Criar o Destino no ZapHook

Agora, vamos apontar o WhatsApp para o seu Langflow.

1. Acesse o painel do ZapHook e vá para Destinations.
2. Clique em Create Destination.
3. Configurações Básicas (Tab Basic):
   * Channel: Selecione seu canal de WhatsApp.
   * Name: Ex: "Agente Langflow".
   * Endpoint URL: Cole a URL que você copiou do componente Webhook do Langflow no Passo 1.
   * HTTP Method: Selecione `POST`.

***

### Passo 3: Mapeamento de dados&#x20;

<figure><img src="../.gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>

O Langflow precisa receber os dados de forma limpa para facilitar o uso dentro dos Prompts. Vamos transformar o JSON complexo do WhatsApp em algo simples.

1. No ZapHook, ainda na criação do Destino, clique na aba Mapeamento de Campos.
2. Cole o seguinte JSON no editor da esquerda:

```
{
  "sender_phone": "",
  "sender_name": "",
  "message_text": ""
}
```

3.  Use a lista de Available Placeholders à direita:

    * Coloque o cursor dentro das aspas de `"input_value"`.
    * Selecione Texto da mensagem (Message Text).
    * _(Opcional)_ Se quiser enviar o telefone para identificar o usuário, adicione um campo `"sender_phone"`.

    _O JSON final deve ficar assim:_

```
{
  "sender_phone": "{{entry[0].changes[0].value.metadata.display_phone_number}}",
  "sender_name": "{{entry[0].changes[0].value.contacts[0].profile.name}}",
  "message_text": "{{entry[0].changes[0].value.messages[0].text.body}}"
}
```

4. Clique em Create Destination.

***

### Passo 4: Validando a integração (Disparo de teste)

<figure><img src="../.gitbook/assets/image (17).png" alt=""><figcaption></figcaption></figure>

Agora que o destino está configurado, vamos testar a conexão diretamente pelo painel do ZapHook, sem precisar abrir terminal ou usar códigos.

1. Disparar o Teste no ZapHook:
   * No menu lateral, vá para a aba Destinos (Destinations).
   * Localize o cartão do destino que você acabou de criar (ex: "Teste de Webhook").
   * Clique no botão Testar (ícone de tubo de ensaio).
   * _O sistema exibirá uma confirmação de que o evento foi enviado com sucesso._
2. Verificar a Recepção no Langflow:
   * Volte para a aba do seu navegador onde o Langflow está aberto.
   * Localize o componente Webhook no seu fluxo.
   * Se a integração funcionou, o componente terá sido executado.
3. Inspecionar os Dados (Logs):

<figure><img src="../.gitbook/assets/image (18).png" alt=""><figcaption></figcaption></figure>

* Clique no ícone de Logs ou Output (dependendo da versão do Langflow, pode ser um ícone de 👁️na base do componente).
* Uma janela se abrirá mostrando o JSON recebido.
* Sucesso: Verifique se os campos `sender_phone`, `sender_name` e `message_text` estão visíveis e preenchidos com os dados de teste enviados pelo ZapHook.

<figure><img src="../.gitbook/assets/image (19).png" alt=""><figcaption></figcaption></figure>

> Nota: O botão "Testar" do ZapHook envia um payload de exemplo baseado no seu Mapeamento de Campos. Se você vir os dados no Langflow, sua "estrada" entre o WhatsApp e a IA está pavimentada e pronta para uso!

### Passo 5: Consumindo os dados (Componente Parser)

O componente Webhook entrega todos os dados juntos em um único pacote (JSON). Para utilizar o telefone, o nome e a mensagem separadamente no seu fluxo, precisamos extraí-los.

A maneira mais eficiente de fazer isso no Langflow é utilizando o componente Parser (encontrado na categoria _Processing_).

1. Adicione os Parsers: Arraste um componente Parser para o canvas para cada informação que você deseja extrair (neste exemplo, usamos 3).
2. Conecte os Dados: Puxe a linha da saída Data do seu Webhook e conecte-a na entrada Data or DataFrame de _todos_ os Parsers.
3.  Configure os Templates: Cada Parser vai funcionar como um "filtro" para pegar uma chave específica do JSON que o ZapHook enviou. No campo Template de cada Parser, digite o nome da variável entre chaves:

    * Parser 1 (Para o Nome): Digite `{sender_name}`
    * Parser 2 (Para o Telefone): Digite `{sender_phone}`
    * Parser 3 (Para a Mensagem): Digite `{message_text}`

    > Dica: Os nomes dentro das chaves `{ }` devem ser _exatamente iguais_ aos que configuramos no Mapeamento do ZapHook.
4. Pronto para uso: Agora, a saída Parsed Text de cada componente contém apenas a informação limpa (ex: apenas o texto da mensagem). Você pode conectar o `Parsed Text` da mensagem diretamente no seu Prompt ou Chat Input, e usar o telefone para consultar um banco de dados, por exemplo.

***

### Passo 6: Teste real e próximos passos

Agora que o Langflow já sabe interpretar os dados, o "trabalho de encanamento" está concluído. A partir daqui, a lógica da inteligência artificial é com você!

Você pode conectar a saída dos seus _Parsers_ a modelos da OpenAI, Anthropic, ou criar sistemas RAG (Retrieval-Augmented Generation) complexos.

Hora do Teste Final (End-to-End):

1. Garanta que seu fluxo no Langflow esteja rodando.
2. Pegue seu celular e envie uma mensagem de WhatsApp para o número que você conectou ao ZapHook.
3. Observe o Langflow: os componentes devem ativar conforme a mensagem passa por eles.

⚠️ A mensagem não chegou? Se funcionou no "Teste" do painel do ZapHook, mas não funciona com a mensagem real do celular, provavelmente você esqueceu de configurar a URL do Canal lá no painel da Meta.

O WhatsApp (Meta) precisa saber para onde enviar as mensagens.

* Lembre-se de copiar a Webhook URL do seu Canal no ZapHook.
* Cole-a no campo "URL de retorno de chamada" dentro do Painel de Apps da Meta.

> Precisa de ajuda com a Meta? Volte ao nosso \[Guia de Início Rápido] para ver o passo a passo de como conectar a URL do ZapHook no painel do Facebook Developers .

***

Parabéns! Você acabou de criar uma ponte profissional entre o WhatsApp Oficial e seus Agentes de IA, sem precisar programar servidores ou APIs complexas.

### Passo 7: Monitoramento e debug

Acesse a aba Monitoramento no menu lateral para acompanhar o status de cada mensagem enviada ao Langflow.

* Verifique o Código de Resposta:
  * 🟢 200 ou 202: Sucesso na conexão. Se o bot não responder, revise a lógica interna do seu fluxo no Langflow.
  * 🔴 4xx ou 5xx: Falha. Geralmente indica URL errada ou JSON mal formatado.
* Investigue o Erro: Clique no botão Detalhes ao lado de qualquer evento para ver exatamente o JSON que foi enviado e a mensagem de erro que o Langflow retornou. Isso elimina a adivinhação na hora de corrigir problemas.

#### Solução de Problemas Comuns

* Erro "Connection Refused" (Langflow Local): O ZapHook está na nuvem e não consegue ver o seu computador (`localhost:7860`). Solução: Use o Ngrok. Rode `ngrok http 7860`, copie a URL gerada (ex: `https://abc-123.ngrok-free.app`) e use essa URL no destino do ZapHook.
* Erro de Formato JSON: Se o Langflow não entender a mensagem, verifique o Passo 3. Certifique-se de que as aspas do JSON não foram quebradas durante o mapeamento.
