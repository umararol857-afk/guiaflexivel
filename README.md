# Guia Flexível de Treinos — cópia local corrigida

Cópia do funil `personal.guiaflexivel.com` corrigida para rodar **localmente** (Live Server do VS Code, `file://` ou qualquer servidor estático).

---

## Como abrir

1. Extraia o ZIP em qualquer pasta.
2. Abra a pasta no VS Code e clique em **Go Live** (extensão Live Server) — ou execute `python3 -m http.server` na pasta.
3. Acesse `index.html`.

Funciona também abrindo o arquivo direto (duplo clique, `file://`) e em subpastas (ex.: `http://127.0.0.1:5500/pasta/index.html`).

---

## Por que a página ficava branca

O site é um app **Next.js** que monta todo o conteúdo no navegador. Duas coisas quebravam fora do domínio original:

1. **Bloqueio por domínio (causa principal).** O código do funil só renderizava o conteúdo quando a página estava em `personal.guiaflexivel.com` ou `inlead.digital`. Em `localhost`, `127.0.0.1` ou `file://` ele caía no estado “vazio” — resultado: tela branca, sem nenhum erro visível no console.
2. **Chunks carregados do CDN original.** O runtime do Next apontava o `publicPath` para `https://inlead.digital/_next/`, e as partes dinâmicas (blocos de preço, carregamento, gráficos etc.) eram buscadas no servidor do cliente. Sem internet ou fora do domínio, partes do funil não carregavam.

## O que foi corrigido

| # | Correção | Onde |
|---|---|---|
| 1 | Liberado o render em `localhost`, `127.0.0.1`, `0.0.0.0` e `file://` | `js/70165-a066f89ad0cfd207.js` |
| 2 | `assetPrefix` trocado para caminho relativo (`"."`) → o site não depende mais do CDN do domínio original | `index.html` (`__NEXT_DATA__`) |
| 3 | Todos os chunks dinâmicos do build (1.952 arquivos) baixados para a pasta local `_next/` | `_next/**` |
| 4 | **Checkout final** trocado para `https://pay.wiapy.com/FphgXzAqTBUr` (8 ocorrências: 2 blocos de preço + 2 botões, em “Guia de Treinos” e “Treino em Casa”) | `index.html` (payload do funil, re-criptografado) |
| 5 | Scripts de rastreamento (GTM, Meta Pixel, UTMify) não são injetados em localhost/`file://`, para não sujar os pixels com eventos de teste | `js/[...all]-eba7f8e81040b596.js` |

O link de checkout é o mesmo, mas o funil acrescenta automaticamente os parâmetros de rastreio da URL atual, por exemplo:
`https://pay.wiapy.com/FphgXzAqTBUr?step=zOXf0L&utm_source=...` — comportamento normal da plataforma.

---

## Observações

- **Imagens:** continuam hospedadas nos CDNs do funil (`cloudfront.inlead.cloud`, `media.inlead.cloud`, `d9aloqs890lqz.cloudfront.net`), como no export original — precisa de internet para exibi-las. Se quiser, é possível baixá-las e deixar o site 100% offline.
- **Fontes:** locais (`fonts/`).
- **Erros no console** de `google.com`, `doubleclick`, `api6.ipify.org` ou `localhost:3001` podem ser ignorados: são rastreadores externos do funil.
- **Testado** em Chromium headless: abertura do funil, navegação por vários passos e clique final de checkout indo para a página do `pay.wiapy.com`.

## Arquivos alterados em relação ao ZIP original

```
index.html                      (payload do funil re-criptografado + assetPrefix relativo)
js/70165-a066f89ad0cfd207.js    (liberação do domínio)
js/[...all]-eba7f8e81040b596.js (guard dos rastreadores em ambiente local)
_next/**                        (novo — chunks dinâmicos do build)
README.md                       (novo)
```
