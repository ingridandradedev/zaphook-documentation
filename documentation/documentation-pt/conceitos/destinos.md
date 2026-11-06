---
description: >-
  Configure webhooks e APIs de destino para receber eventos do WhatsApp. Suporte
  a retries, autenticação e transformação de payloads.
icon: arrows-turn-to-dots
---

# Destinos

Um **destination** (destino) representa o endpoint para onde os eventos processados pelo ZapHook serão enviados.

Depois que um canal recebe e processa eventos do WhatsApp (mensagens, status, mídias, etc.), esses dados precisam ser entregues ao seu sistema, e é exatamente isso que os destinations fazem.

Na prática, um destino funciona como uma **saída configurável do pipeline de eventos**, permitindo que você encaminhe os dados para qualquer API externa, como:

* Backends próprios
* CRMs (ex: HubSpot, RD Station)
* Sistemas de automação
* Serviços de IA
* Webhooks internos

Cada canal pode ter um ou mais destinos associados.

***

### Como funciona

O fluxo de entrega de eventos com destino ocorre da seguinte forma:

1. Um canal recebe um evento da Meta (ex: mensagem recebida)
2. O ZapHook processa e normaliza o payload
3. O evento é encaminhado para todos os destinations ativos do canal
4. Cada destino realiza uma requisição HTTP para o endpoint configurado
5. Em caso de falha, políticas de retry podem ser aplicadas

Esse modelo permite que um único evento seja distribuído para múltiplos sistemas de forma independente.

***

### Principais características

Os destino foram projetados para oferecer flexibilidade total na integração com sistemas externos.

#### Entrega via HTTP

* Suporte a métodos HTTP (ex: POST)
* Envio de payloads estruturados ou brutos
* Integração com qualquer API REST

#### Mapeamento de dados (Field Mapping)

* Permite transformar o payload antes do envio
* Renomear campos
* Filtrar dados desnecessários
* Adaptar o formato para o destino

Isso elimina a necessidade de tratamento adicional no seu backend.

***

#### Payload bruto (Raw Payload)

* Opção de enviar o payload original da Meta
* Sem normalização ou transformação
* Útil para integrações que precisam do formato nativo

***

#### Autenticação

* Suporte a configuração de autenticação customizada
* Headers HTTP personalizados
* Integração com APIs que exigem tokens ou chaves

***

#### Retry automático

* Configuração de políticas de retry
* Reenvio automático em caso de falha
* Aumenta a confiabilidade da entrega

***

#### Timeout configurável

* Controle do tempo máximo de resposta do endpoint
* Evita travamentos em integrações lentas

***

#### Ativação e controle

* Destinations podem ser ativados ou desativados (`is_active`)
* Alterações não afetam outros destinations do mesmo canal

***

### Casos de uso

Os destinations permitem adaptar o ZapHook a diferentes arquiteturas e necessidades.

#### Integração com backend próprio

Encaminhe eventos diretamente para sua API e processe conforme sua lógica de negócio.

***

#### Envio para múltiplos sistemas

Um único evento pode ser enviado para:

* CRM
* Sistema interno
* Ferramenta de analytics

Tudo ao mesmo tempo.

***

#### Automação de fluxos

Utilize destinations para acionar automações externas com base em eventos do WhatsApp.

***

#### Integração com IA

Envie mensagens e áudios transcritos diretamente para:

* Chatbots
* Agentes de IA
* Sistemas de processamento de linguagem

***

### Estrutura de um destination

Cada destination possui os seguintes elementos principais:

* `endpoint_url`: URL do endpoint que receberá os eventos
* `http_method`: método HTTP utilizado
* `headers`: headers customizados
* `auth_config`: configuração de autenticação
* `field_mapping`: regras de transformação de dados
* `retry_config`: política de retry
* `timeout_seconds`: tempo limite da requisição
* `raw_payload`: define se o payload será bruto ou processado
* `is_active`: status do destination

***

### Comportamento de entrega

* Todos os destinations ativos de um canal recebem os eventos
* A entrega é independente entre destinations
* Falhas em um destination não afetam os demais
* O retry é aplicado individualmente por destination

***

### Considerações

* Destinations são essenciais para conectar o ZapHook ao seu sistema
* Permitem desacoplar completamente o recebimento de eventos da lógica de processamento
* Reduzem a necessidade de múltiplos webhooks e integrações complexas

***

### Recomendação

Utilize destino para centralizar a lógica de entrega e transformação de eventos no ZapHook.

Isso permite:

* simplificar seu backend
* reduzir código de integração
* aumentar a confiabilidade da entrega
