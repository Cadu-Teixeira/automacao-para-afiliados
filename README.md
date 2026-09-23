# Automação para Afiliados

Sistema de automação 100% construído em [n8n](https://n8n.io) self-hosted que coleta ofertas de afiliado de 4 marketplaces (Shopee, Mercado Livre, Amazon, Leroy Merlin) e posta automaticamente em um grupo de WhatsApp, sem intervenção manual no dia a dia.

> 📊 O desenho completo do sistema (fluxogramas, linha do tempo, padrões de workflow) está em [`ARQUITETURA.md`](ARQUITETURA.md).

## O problema

Curar e postar ofertas manualmente todo dia não escala: exige estar disponível o dia inteiro, é fácil perder a mão na frequência (postar rápido demais aumenta o risco de banimento em ferramentas de WhatsApp não-oficiais) e a qualidade da legenda varia dependendo do humor de quem está postando.

## A solução

Um sistema onde cada marketplace roda como uma automação **independente**, sem orquestrador central:

- **Sem ponto único de falha**: se uma fonte trava ou é desativada, as outras continuam postando normalmente.
- **Dois padrões de coleta**, escolhidos conforme a fonte: busca ao vivo direto na API oficial (quando existe uma com dados de comissão/vendas em tempo real), ou fila abastecida em lote por colheita externa (quando não há API viável).
- **Revezamento programado**: as fontes disparam em horários deslocados entre si, resultando em 1 post a cada 45 minutos, o dia inteiro — cadência pensada especificamente para não levantar bandeira de spam.
- **Rotação de categoria e deduplicação**: nada se repete nos últimos 14 dias, e a categoria do produto é alternada a cada post pra manter o feed variado.
- **Geração de legenda persuasiva**: hooks, CTAs e formatação variam a cada post (nada de mensagem robotizada e idêntica todo dia).
- **3 travas anti-spam**: idempotência nos nodes de efeito colateral, validação de dado completo antes de postar, e grafo de execução estritamente linear (sem ciclos) — decisões que vieram de um incidente real de over-posting, corrigido e documentado.

## Stack

n8n (self-hosted) · APIs de afiliados (GraphQL, REST) · Webhooks · Data tables como fila/estado leve · Evolution API (WhatsApp) · automação de navegador para coleta em fontes sem API pública.

## Sobre este repositório

Este é um projeto real, em produção, gerando posts diariamente. O código-fonte completo dos workflows (regras de negócio, credenciais, lógica de categorização e priorização) é privado. Este repositório público documenta a **arquitetura e as decisões técnicas** — é a parte que interessa pra entender como o sistema foi pensado e construído, sem expor a operação em si.
