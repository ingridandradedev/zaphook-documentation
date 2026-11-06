---
description: Compreenda em detalhes o funcionamento de um canal de coexistência no ZapHook
---

# Canal de coexistência

### isão geral

O **canal de coexistência** é o modelo recomendado de integração no ZapHook.

Ele foi projetado para simplificar a conexão com o WhatsApp Business sem exigir a configuração tradicional da Meta Cloud API, como criação de aplicativos, configuração manual de webhooks ou gerenciamento direto de credenciais técnicas.

Nesse modelo, o ZapHook permite que você conecte números de WhatsApp utilizando apenas um **Business Manager** e um número válido, reduzindo significativamente a complexidade de setup.

Além disso, o canal de coexistência permite que o ZapHook opere **em conjunto com outras integrações existentes**, sem necessidade de substituição imediata da sua infraestrutura atual.

***

### Como funciona

No modo de coexistência, o fluxo de conexão e operação ocorre da seguinte forma:

1. Um número de WhatsApp é conectado ao ZapHook (diretamente ou via fluxo de onboarding)
2. O ZapHook estabelece a comunicação com a infraestrutura da Meta
3. Os eventos (mensagens, status, mídias, etc.) passam a ser processados pela plataforma
4. Os dados são normalizados e entregues ao seu sistema via webhook ou API

Diferente do modelo tradicional, você não precisa configurar manualmente endpoints na Meta Developers — o ZapHook abstrai essa camada.&#x20;

{% hint style="info" %}
Configure seu primeiro canal de coexistência seguindo o [guia passo a passo](../../how-to/gerar-token-permanente.md).
{% endhint %}

***

### Principais características

O canal de coexistência foi projetado para reduzir fricção e acelerar integrações.

#### Setup simplificado

* Não requer criação de aplicativo na Meta Developers
* Não exige configuração manual de webhook
* Não depende de validação manual de tokens ou assinaturas

#### Menor dependência técnica

* Elimina a necessidade de lidar diretamente com a API da Meta
* Reduz erros comuns de configuração
* Permite que times não técnicos consigam conectar contas

#### Compatibilidade com outros sistemas

* Pode ser utilizado junto com integrações existentes
* Não exige migração imediata de infraestrutura
* Permite arquiteturas híbridas

#### Foco em escala

* Ideal para múltiplos clientes (multi-tenant)
* Permite onboarding rápido e repetível
* Reduz custo operacional de suporte e configuração

***

### Casos de uso

O canal de coexistência é especialmente indicado para:

#### Plataformas SaaS

Permite que cada cliente conecte seu próprio número de WhatsApp de forma simples, sem necessidade de suporte técnico avançado ou configuração manual.

#### Produtos com onboarding automatizado

Facilita a geração de fluxos de conexão onde o próprio usuário final realiza a integração em poucos passos.

#### Integrações de baixo atrito

Ideal quando o objetivo é minimizar tempo de setup e reduzir barreiras de entrada.

#### Ambientes com infraestrutura existente

Pode ser adotado sem interromper sistemas já em funcionamento, permitindo uma transição gradual.

***

### Diferenças em relação ao setup manual

Enquanto o **setup manual** segue o modelo tradicional da Meta (com criação de apps, configuração de webhook e gestão de credenciais), o canal de coexistência abstrai toda essa complexidade.

Principais diferenças:

* Não exige acesso ao Meta Developers
* Não requer configuração manual de endpoints
* Reduz significativamente o tempo de implementação
* Diminui a necessidade de conhecimento técnico específico

***

### Considerações

* O canal de coexistência prioriza simplicidade e velocidade de integração
* É a melhor escolha para a maioria dos cenários modernos
* Pode ser utilizado como ponto de entrada antes de evoluir para arquiteturas mais avançadas

***

### Recomendação

Sempre que possível, utilize o **canal de coexistência** como padrão.

Ele oferece o melhor equilíbrio entre:

* simplicidade
* escalabilidade
* velocidade de implementação
