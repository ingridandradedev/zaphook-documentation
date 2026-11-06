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

# Integração ZapHook + Make

{% embed url="https://youtu.be/YaHiSZKUZLc?si=kVLqBzWr9460MR2S" %}

Se você já tentou integrar a API Oficial do WhatsApp (Cloud API) diretamente sabe a dor de cabeça que é: lidar com JSONs gigantes, configurar verificação de tokens, tratar mídias complexas e manter um servidor rodando 24/7 só para "escutar" mensagens.

Neste guia você vai pular toda essa complexidade. Vamos conectar o WhatsApp ao **Make** usando o **ZapHook**, seu _Webhook as a Service_ que limpa os dados e entrega apenas o que importa para a sua automação.

### O que você vai precisar

Para seguir este passo a passo, certifique-se de ter:

1. **Conta na Meta for Developers:** Com um número de WhatsApp Business configurado.
2. **Conta no ZapHook:** Você pode criar gratuitamente em [zaphook.cloud](https://zaphook.cloud/)
3. **Conta no Make (antigo Integromat):** Para criar o fluxo de automação
4. **Chave de API de IA:** Neste exemplo, usaremos o **OpenRouter** (que possui modelos gratuitos), mas você pode usar OpenAI ou similares.
5. **Blueprint do Make:** O arquivo JSON do fluxo pronto para importar (disponibilizado no material de apoio do vídeo). Baixe aqui:

### Passo 1: Criando a Porta de Entrada no ZapHook

O primeiro passo é dizer ao ZapHook para "ouvir" o WhatsApp por você.

1. Acesse o painel do **ZapHook** e vá até a aba **Canais**.
2. Clique em **Criar Novo Canal** e dê um nome ao seu projeto.
3. O sistema vai gerar dois dados cruciais:
   * **Webhook URL:** O endereço para onde o WhatsApp enviará as mensagens.
   * **Verify Token:** A senha que valida a conexão segura com a Meta.

> **Nota:** Copie esses dados, pois vamos usá-los agora mesmo.

***

### Passo 2: Configurando a Meta (Facebook)

Agora precisamos conectar a Meta ao canal que acabamos de criar.

1. No painel da **Meta for Developers**, acesse seu aplicativo e vá em **WhatsApp > Configuração**.
2. Na área de **Webhooks**, clique em editar.
3. Cole a **URL de Callback** e o **Verify Token** que o ZapHook gerou para você.
4. Clique em **Verificar e Salvar**. Se tudo estiver certo, a janela fechará sem erros.
5. **Importante:** Role a página até "Campos de Webhook" e inscreva-se no evento **messages**. Sem isso, o WhatsApp não envia nada!.

***

### Passo 3: Configurando o "Cérebro" no Make

Para não perder tempo, não vamos construir do zero.

1. No **Make**, crie um novo cenário.
2. Clique nos três pontinhos (...) e selecione **Import Blueprint**. Faça o upload do arquivo JSON.
3. O fluxo aparecerá pronto. Ele consiste em:
   * **Webhook:** Recebe os dados limpos.
   * **Router:** Separa mensagens de texto de outros tipos.
   * **OpenRouter (IA):** Gera a resposta inteligente.
   * **HTTP Request:** Envia a resposta de volta para o WhatsApp do cliente via API da Meta.

**Configuração Rápida:**

* No módulo **OpenRouter**, adicione sua chave de API.
* Nos módulos **HTTP (Meta)**, preencha com seu Phone ID e System User Token da Meta.
* No módulo inicial **Webhook**, clique em "Add", dê um nome e **copie a URL gerada pelo Make**.

***

### Passo 4: Conectando o ZapHook ao Make (A Mágica do Mapeamento)

Agora vamos enviar os dados do ZapHook para o Make.

1. Volte ao **ZapHook** e vá na aba **Destinos**.
2. Crie um novo destino e cole a **URL do Webhook do Make** que você copiou.
3. **Mapeamento de Campos:** Aqui está o segredo. Em vez de enviar o JSON confuso da Meta, vamos usar o editor visual do ZapHook para enviar dados limpos.
4. Arraste os blocos dinâmicos para montar seu JSON simplificado:
   * nome → Bloco **Nome do Remetente**
   * telefone → Bloco **Telefone do Remetente**
   * mensagem → Bloco **Texto da Mensagem**
   * transcricao → Bloco **Transcrição** (Sim, o ZapHook transcreve áudio nativamente!).
5. Salve o destino.

***

### Passo 5: Testando na Prática

Com tudo conectado, é hora da verdade.

1. Envie uma mensagem no WhatsApp para o seu número de teste (ex: "Oi, como você pode me ajudar?").
2. O ZapHook receberá a mensagem, limpará os dados e enviará para o Make.
3. O Make processará a IA e responderá no seu WhatsApp em segundos.

Você pode acompanhar tudo pelo painel de **Monitoring** do ZapHook. Se ver um status **200**, significa sucesso total!.

Viu como foi rápido? Sem configurar servidores Linux, sem decifrar documentação complexa e com segurança garantida. O **ZapHook** cuida da infraestrutura para você focar apenas na lógica da sua automação.

🚀 **Quer testar agora?** Crie sua conta gratuita em [zaphook.cloud](http://zaphook.cloud/) e comece a integrar em minutos.
