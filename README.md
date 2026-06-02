# VSL Estática — Michael Collins (folder-per-funil)

Páginas de VSL 100% HTML+CSS, **sem Atomicat, sem builder, sem interceptor de clique**.
Mesmos scripts de tracking que você usa hoje (Clarity + UTMify latest.js + UTMify pixel.js),
mas com botão `<a>` nativo → clique instantâneo (~36ms medido).

## Estrutura
```
vsl-static/                 ← repo (raiz = domínio do CNAME)
├── CNAME                   → lp.michaelcollins.online  (TROCAR pelo seu subdomínio)
├── assets/                 → imagens COMPARTILHADAS (1 cópia só)
│   ├── media.png  (badges "We're in the media")
│   ├── author.png (foto Michael Collins)
│   └── signature.png
├── vsl/index.html          → lp.michaelcollins.online/vsl/
├── 147/index.html          → lp.michaelcollins.online/147/   (mesma VSL, slug p/ outra campanha)
├── privacy.html            → /privacy.html
└── terms.html              → /terms.html
```

## Como adicionar um novo funil
1. Copie uma pasta existente: `cp -r vsl/ NOVOSLUG/`
2. Edite `NOVOSLUG/index.html` → bloco `FUNNEL_CONFIG` no topo do `<head>`:
   ```js
   window.FUNNEL_CONFIG = {
     checkoutLink: "https://d2-info.mycartpanda.com/ckt/XXXX",
     ctaText: "SEU CTA",
     revealAtSeconds: 1382   // backup; primário = VTurb esconderbutton
   };
   ```
3. Se o VÍDEO for outro: troque o `id` do `<vturb-smartplayer>` e o `src` do `player.js`
   (2 lugares: o `<link rel=preload>` e o loader no fim do body).
4. Imagens diferentes? Coloque em `assets/` e referencie `../assets/seuarquivo.png`.

## Scripts incluídos (iguais ao Atomicat, menos o builder)
| Script | Função |
|--------|--------|
| Microsoft Clarity (`wx1hiq2rnm`) | heatmap / gravações (diagnóstico) |
| VTurb player (A/B test) | o vídeo |
| UTMify `latest.js` | captura UTM (não bloqueia clique) |
| UTMify `pixel.js` (`6a1a4722a06388c227fc3c46`) | pixel obrigatório em todas as páginas |

## Botão e timing
- Botão = `<a href>` nativo → navegação do browser (impossível interceptar)
- Reveal aos **1382s (≈23min)** via VTurb removendo a classe `esconderbutton` (primário)
- Backup: poller pela API do smartplayer
- UTMs do anúncio são encaminhados pro checkout automaticamente

## QA / teste local
```bash
cd vsl-static
python -m http.server 8899
# http://127.0.0.1:8899/vsl/?reveal=1   → mostra botão na hora
# http://127.0.0.1:8899/147/?reveal=1
```
- `?reveal=1` mostra o botão imediatamente (testar clique sem esperar 23min)
- `?reveal=10` mostra após 10s
- ⚠️ No localhost o VÍDEO não toca (VTurb licencia por domínio) — layout/botão/UTM testam OK

## ⚠️ Antes do deploy (3 passos)
1. **Trocar o CNAME** pelo subdomínio escolhido (ex: `lp.michaelcollins.online`)
2. **DNS** (Cloudflare): CNAME do subdomínio → GitHub Pages / Cloudflare Pages
3. **Autorizar o domínio no painel VTurb** (senão o vídeo fica preto) ← make-or-break

## Validar o reveal real (no domínio autorizado)
Dá play, arrasta a barra do vídeo até ~23min → o botão verde deve aparecer sozinho
(VTurb removendo `esconderbutton`). Se não aparecer, o backup poller cobre.

## Checkout
`https://d2-info.mycartpanda.com/ckt/xj4vgQ` — editável no `FUNNEL_CONFIG` de cada funil.
