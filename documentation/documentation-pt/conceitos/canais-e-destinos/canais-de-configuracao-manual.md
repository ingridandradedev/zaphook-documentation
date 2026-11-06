# Canais de configuração manual

### Visão geral

O **canal com setup manual** segue o modelo tradicional de integração com a **WhatsApp Cloud API da Meta**, onde a configuração é realizada diretamente pelo desenvolvedor no ambiente da Meta Developers.

Nesse modelo, o ZapHook atua como a camada de processamento e entrega de eventos, mas a responsabilidade pela configuração inicial,  incluindo criação de aplicativo, definição de webhook e gestão de credenciais,  permanece com você.

Esse tipo de canal oferece maior controle sobre a integração, sendo mais indicado para cenários que exigem personalização ou já possuem uma infraestrutura estabelecida.

***

### Como funciona

No setup manual, o fluxo de configuração e operação ocorre da seguinte forma:

1. Você cria um aplicativo no Meta Developers
2. Configura o produto do WhatsApp dentro do app
3. Define o webhook apontando para a URL fornecida pelo ZapHook
4. Configura o `verify_token` para validação do webhook
5. Gera e gerencia o `access_token` da API
6. Após a configuração, a Meta passa a enviar eventos para o ZapHook
7. O ZapHook processa, normaliza e encaminha os dados para o seu sistema

Nesse modelo, o ZapHook entra após a configuração inicial, atuando como intermediário entre a Meta e sua aplicação.

{% hint style="info" %}
Configure seu primeiro canal de coexistência seguindo o [guia passo a passo](../../how-to/gerar-token-permanente.md).
{% endhint %}

***

### Principais características

O setup manual é mais próximo da integração nativa com a Meta e oferece maior controle técnico.

#### Controle total da configuração

* Criação e gestão do app na Meta Developers
* Definição manual do webhook
* Controle sobre tokens, permissões e configurações
* Possibilidade de customizações mais específicas

#### Integração direta com a Meta

* Uso completo da WhatsApp Cloud API
* Acesso direto aos recursos da plataforma da Meta
* Maior transparência sobre o fluxo de dados

#### Maior responsabilidade operacional

* Necessidade de validar assinaturas de webhook (quando aplicável)
* Gestão de expiração e rotação de tokens
* Manutenção da configuração no ambiente da Meta

***

### Casos de uso

O canal com setup manual é indicado para cenários onde há necessidade de maior controle ou integração mais profunda.

#### Integrações avançadas

Projetos que exigem controle detalhado sobre configurações da API da Meta ou comportamentos específicos.

#### Infraestrutura própria

Sistemas que já possuem integração com a Meta e desejam utilizar o ZapHook como camada adicional de processamento.

#### Times técnicos

Equipes com experiência na configuração da Meta Cloud API e que preferem manter controle direto sobre a integração.

***

### Diferenças em relação ao canal de coexistência

Enquanto o **canal de coexistência** simplifica completamente o processo de integração, o setup manual segue o fluxo tradicional da Meta.

Principais diferenças:

* Requer criação de app na Meta Developers
* Exige configuração manual de webhook
* Necessita gerenciamento de tokens e permissões
* Maior complexidade de setup
* Mais controle sobre a integração

***

### Considerações

* O setup manual exige maior conhecimento técnico
* O tempo de implementação tende a ser maior
* Erros de configuração são mais comuns nesse modelo
* Pode exigir manutenção contínua da integração

***

### Recomendação

Utilize o **setup manual** apenas quando houver necessidade específica de controle ou compatibilidade com configurações já existentes.

Para a maioria dos casos — especialmente novos projetos e SaaS — o uso do [**canal de coexistência**](./#coexistencia-recomendado) é mais simples, rápido e eficiente.
