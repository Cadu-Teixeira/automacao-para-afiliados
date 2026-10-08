# Automação para Afiliados

Sistema de automação 100% construído em **n8n self-hosted** que coleta ofertas de afiliado de **5 marketplaces** (Shopee, Mercado Livre, Amazon, Leroy Merlin e Magalu) e posta automaticamente em um grupo de WhatsApp — além de uma esteira própria de **cupons de desconto** — sem intervenção manual no dia a dia.

📊 O desenho completo do sistema (fluxogramas, linha do tempo, padrões de workflow) está em [ARQUITETURA.md](ARQUITETURA.md).

## O problema

Curar e postar ofertas manualmente todo dia não escala: exige estar disponível o dia inteiro, é fácil perder a mão na frequência (postar rápido demais aumenta o risco de banimento em ferramentas de WhatsApp não-oficiais) e a qualidade da legenda varia dependendo do humor de quem está postando.

## A solução

Um sistema onde cada marketplace roda como uma automação independente, sem orquestrador central:

- **Sem ponto único de falha:** se uma fonte trava ou é desativada, as outras continuam postando normalmente.
- **Dois padrões de coleta, escolhidos conforme a fonte:** busca ao vivo direto na API oficial (quando existe uma com dados de comissão/vendas em tempo real), ou fila abastecida em lote por colheita externa (quando não há API viável).
- **Revezamento programado:** as 5 fontes disparam em horários deslocados entre si — **1 post por hora, das 8h às 22h, 15 posts por dia** — cadência pensada para não levantar bandeira de spam.
- **Esteira de cupons separada:** um único workflow publica cupons reais de Mercado Livre, Shopee e Amazon a cada 45 minutos, com instrução de uso por plataforma e texto especial em datas de campanha (ex.: 10.10, Dia das Crianças).
- **Seleção ponderada por categoria:** categorias com mais conversão (beleza, eletrônicos) recebem mais peso; a categoria nunca se repete em dois posts seguidos e nada se repete nos últimos 14 dias.
- **Legenda persuasiva:** ganchos, CTAs e formatação variam a cada post (nada de mensagem robotizada e idêntica todo dia).
- **Travas anti-spam:** idempotência nos nodes de efeito colateral, validação de dado completo antes de postar e grafo de execução estritamente linear (sem ciclos) — decisões que vieram de um incidente real de excesso de postagens, corrigido e documentado.
- **Governança dos workflows:** nada é duplicado para mudar; cada alteração vira uma versão no histórico do n8n, com marcação visual de qual configuração está ativa.

## Fontes

| Marketplace | Padrão de coleta |
|---|---|
| Shopee | Busca ao vivo (API GraphQL de afiliados) |
| Mercado Livre | Fila abastecida em lote (foco em produtos com comissão extra) |
| Amazon | Fila abastecida em lote |
| Leroy Merlin | Fila abastecida em lote |
| Magalu | Fila abastecida em lote (vitrine de afiliado) |
| Shein | 🔜 Em integração |

## Stack

n8n (self-hosted, Docker) · APIs de afiliados (GraphQL, REST) · Webhooks · Data tables como fila/estado leve · Evolution API (WhatsApp) · LLM para variação de legendas · automação de navegador para coleta em fontes sem API pública.

## Sobre este repositório

Este é um projeto real, em produção, gerando posts diariamente. O código-fonte completo dos workflows (regras de negócio, credenciais, lógica de categorização e priorização) é privado. Este repositório público documenta a arquitetura e as decisões técnicas — é a parte que interessa pra entender como o sistema foi pensado e construído, sem expor a operação em si.

---
Feito por **Cadu Teixeira** · [caduteixeira.com](https://caduteixeira.com)
