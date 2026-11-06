# Table of contents

* [Seja bem-vindo ao ZapHook](README.md)

## Para começar

* [Visão geral](para-comecar/visao-geral.md)
* [Quick start: como fazer sua primeira conexão](para-comecar/quickstart.md)

## Conceitos

* [Canais](conceitos/canais-e-destinos/README.md)
  * [Canal de coexistência](conceitos/canais-e-destinos/canal-de-coexistencia.md)
  * [Canais de configuração manual](conceitos/canais-e-destinos/canais-de-configuracao-manual.md)
* [Destinos](conceitos/destinos.md)
* [Monitoramento e logs](conceitos/monitoramento-e-logs.md)

## How to

* [Como criar uma API para receber eventos do WhatsApp no ZapHook](how-to/gerar-token-permanente.md)
* [Conectando seu WhatsApp via coexistência no ZapHook (Guia de integração)](how-to/gerar-token-permanente-1.md)

***

* [Como gerar o Token de Acesso (System User) para o ZapHook](gerar-token-permanente.md)

## CookBook

* [Integração ZapHook + Typebot](cookbook/integracao-zaphook-typebot.md)
* [Integração ZapHook + Langflow](cookbook/integracao-zaphook-langflow.md)
* [Integração ZapHook + Make](cookbook/integracao-zaphook-langflow-1.md)

## Programa de indicação

* [Programa de parcerias ZapHook](programa-de-indicacao/programa-de-parcerias-zaphook.md)

## API Reference

* [Gerando uma API Key no ZapHook](api-reference/gerando-uma-api-key-no-zaphook.md)
* ```yaml
  type: builtin:openapi
  props:
    models: true
    downloadLink: true
  dependencies:
    spec:
      ref:
        kind: openapi
        spec: zaphook-public-api
  ```
