---
icon: pen-to-square
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
    - /broken/spaces/yE16Xb3IemPxJWydtPOj/pages/i73g4LZQanoLj7XtSO18
---

# Monitoramento e logs

<figure><img src="../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

O ZapHook elimina a "caixa preta" das integrações de webhook. Nossa plataforma oferece observabilidade completa ("Observabilidade"), permitindo que você rastreie cada evento desde o momento em que ele sai do WhatsApp até a entrega no seu sistema.

#### 1. Visão Geral do Dashboard

Ao acessar a aba Monitoramento, você tem uma visão macro da saúde da sua operação:

* Métricas em Tempo Real:
  * Total de Eventos Hoje: Volume total de tráfego.
  * Taxa de Sucesso: Porcentagem de entregas bem-sucedidas (Ideal: > 99%).
  * Bem-sucedidos vs. Falhados: Contagem absoluta para identificação rápida de anomalias.
* Filtros Avançados: É possível filtrar os logs por Canal específico ou por Status (ex: ver apenas eventos falhados para correção).

#### 2. Lista de Logs de Eventos

A tabela principal mostra o histórico recente de transações. As colunas mais importantes para o seu diagnóstico são:

* Status: Indica se o ZapHook conseguiu processar a mensagem (`delivered` ou `failed`).
* Código de Resposta: O código HTTP retornado pelo seu sistema (Destino).
  * 🟢 200 ou 202: Sucesso. Seu servidor aceitou o evento.
  * 🔴 4xx ou 5xx: Erro. Seu servidor rejeitou a conexão ou caiu.
* Tempo de Processamento: Quanto tempo (em milissegundos) o ZapHook levou para receber, transformar e entregar o dado (ex: `124ms`).

***

#### 3. Detalhes do Evento (Inspeção Profunda)

Ao clicar no botão Detalhes de qualquer linha, você abre um raio-X completo da transação. Abaixo explicamos cada campo técnico disponível nesta tela:

**Metadados da Entrega**

Informações técnicas sobre a tentativa de envio.

* ID do Evento: Identificador único (UUID) para rastreamento (ex: `2d1d69d4...`).
* Código de Resposta: Confirmação técnica. Um código `202` indica que seu sistema aceitou o pedido para processamento.
* Contagem de Tentativas: Mostra o status do _Smart Retry_.
  * Exemplo: `0 / 3` significa que foi entregue na primeira tentativa (0 retentativas usadas de 3 permitidas). Se houvesse falha, você veria `1 / 3`, `2 / 3`, etc.
* URL de Destino: O endpoint exato para onde enviamos os dados (ex: `https://langflow.../webhook/...`). Útil para verificar se você não enviou para o ambiente errado (Dev vs Prod).

**Inspeção de Payloads (Entrada e Saída)**

Aqui você valida se a transformação de dados (Mapeamento) funcionou como esperado.

* Payload Original: Este é o JSON bruto e completo que a Meta (WhatsApp) enviou para o ZapHook. Contém estruturas aninhadas complexas, chaves de criptografia e metadados da Meta.
  * _Exemplo:_ Objeto contendo `entry`, `changes`, `value`, `messages`, etc.
* Payload Processado: Este é o JSON final que o ZapHook enviou para o seu sistema após aplicar as regras de Mapeamento de Campos.
  * _Nota:_ Se você não configurar nenhum mapeamento, este campo será idêntico ao Payload Original. Se configurar, aqui aparecerá apenas o JSON limpo (ex: `{ "telefone": "...", "texto": "..." }`).

**Corpo da Resposta (Response Body)**

Este é o campo mais valioso para Debug (Correção de Erros). Ele mostra exatamente o que o seu servidor respondeu para o ZapHook.

*   _Exemplo de Sucesso:_

    JSON

    ```
    {"message":"Task started in the background","status":"in progress"}
    ```

    Isso confirma que, no exemplo acima, o Langflow recebeu o dado e iniciou o fluxo corretamente.
* _Exemplo de Erro (Hipotético):_ Se o seu sistema estivesse com erro, você veria algo como: `{"error": "Invalid API Key"}`. Isso permite que você saiba instantaneamente que precisa corrigir a autenticação, sem precisar abrir os logs do seu próprio servidor.
