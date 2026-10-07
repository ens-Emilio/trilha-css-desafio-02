# Clone da página de vídeo do YouTube com Flexbox

Desafio 02 da [Trilha de CSS da DIO](https://www.dio.me/), com foco em **Flexbox**.

> **O desafio:** clonar a página do YouTube com CSS, colocando em prática todos os
> conceitos aprendidos, principalmente sobre **Flexbox**. O design de referência
> está no [Figma](https://www.figma.com/design/lrRWUZPKnqMDZrSDJmZxUS/Desafio-de-Flexbox---DIO).

**Como este projeto foi feito:** o desafio **não tem repositório base** (só existe
o `trilha-css-desafio-01` na organização da DIO), e o Figma bloqueia acesso
automático. Então os dados do protótipo foram **extraídos do próprio Figma**:
texto por OCR do canvas renderizado, cores por amostragem de pixel, proporções
por medição das regiões, e os assets baixados das URLs de imagem do arquivo.
Os números dessa extração estão documentados abaixo.

---

## Resultado

Página de vídeo em reprodução, com cabeçalho, player, informações do vídeo e
barra lateral de recomendados.

### Estrutura

```
trilha-css-desafio-02/
├── index.html
├── assets/
│   ├── css/
│   │   ├── reset.css      Normalização (53 linhas)
│   │   └── styles.css     Estilização completa (810 linhas comentadas)
│   └── images/
│       ├── youtube-logo.png
│       ├── video-principal.png
│       ├── avatar-dio.png
│       ├── avatar-usuario.jpg
│       └── thumb-01..06.png
└── README.md
```

---

## Flexbox: onde cada técnica foi usada

Este é o ponto central do desafio. Cada container de layout é Flexbox, e o
arquivo `styles.css` traz comentários explicando a decisão em cada um.

### 1. Cabeçalho — três grupos, um deles flexível

```css
.cabecalho {
    display: flex;
    align-items: center;
    justify-content: space-between;
}

.cabecalho__centro {
    flex: 1 1 auto;      /* ocupa o espaço que sobra */
    justify-content: center;
}
```

O grupo do centro recebe `flex: 1` e centraliza a busca internamente. Isso
resolve o alinhamento **sem** `position: absolute` e **sem** largura calculada
— que é o problema clássico de centralizar uma barra de busca em um cabeçalho.

### 2. Página — duas colunas com comportamentos diferentes

```css
.pagina    { display: flex; gap: 24px; }
.principal { flex: 1 1 auto; min-width: 0; }   /* cresce */
.lateral   { flex: 0 0 424px; }                /* não cresce, não encolhe */
```

`flex: 0 0 424px` é a abreviação de `flex-grow: 0`, `flex-shrink: 0`,
`flex-basis: 424px`. A lateral mantém a largura mesmo quando falta espaço, e o
vídeo fica com todo o resto.

### 3. `min-width: 0` — o detalhe que mais quebra layouts

Por padrão, um item flex **não encolhe abaixo do tamanho do seu conteúdo**. Um
título longo empurra a coluna e estoura a página horizontalmente. Com
`min-width: 0` no item, ele passa a poder encolher e o texto é cortado com
reticências.

Aplicado em `.principal`, `.cabecalho__centro`, `.busca`, `.busca__campo` e
`.card__info` — todos os lugares onde há texto que pode crescer demais.

### 4. Cards laterais — miniatura fixa + texto flexível

```css
.card       { display: flex; gap: 8px; }
.card__thumb{ flex: 0 0 auto; width: 179px; }  /* não encolhe */
.card__info { flex: 1 1 auto; min-width: 0; }   /* ocupa o resto */
```

O título é limitado a duas linhas com `-webkit-line-clamp: 2`, então cards com
títulos longos e curtos ficam com a mesma altura.

### 5. Barra de ações com quebra automática

```css
.barra-video { display: flex; justify-content: space-between; flex-wrap: wrap; }
```

Em tela larga, canal e ações ficam na mesma linha nas pontas opostas. Quando não
cabe, `flex-wrap: wrap` faz as ações descerem sozinhas, sem sobrepor nada.

### 6. Outros usos

| Elemento | Técnica |
| --- | --- |
| Botões de ícone | `flex` + `align-items`/`justify-content: center` para centralizar o SVG |
| Grupo curtir/não gostei | `flex` com divisor de 1px entre os dois |
| Controles do player | `flex` + `justify-content: space-between` (dois grupos nas pontas) |
| Lista da lateral | `flex-direction: column` com `gap` |
| Badge do sino | `flex` para centralizar o número, com `position: absolute` no canto |

### 7. Responsividade trocando a direção

```css
@media (max-width: 1000px) {
    .pagina { flex-direction: column; }   /* a lateral vai para baixo */
}
@media (max-width: 480px) {
    .card { flex-direction: column; }     /* o card vira uma coluna */
}
```

A quebra principal não é esconder nada: é **mudar a direção do flex**. O mesmo
HTML se reorganiza em três formatos diferentes.

---

## Dados extraídos do protótipo

### Texto (OCR do canvas)

Todo o conteúdo é o do design original:

- **Título:** Semana Front-end | Dia 01: Construindo uma Landing Page no Mundo
  Invertido com HTML e CSS
- **Canal:** DIO — 83,3 mil inscritos
- **Ações:** 2 mil · Não gostei · Compartilhar · Download
- **Meta:** 28.418 visualizações • Transmitido ao vivo em 23 ago. de 2022
- **Descrição:** texto completo sobre a landing page com theme switcher
- **Lateral:** 6 vídeos, com canal, visualizações e data de cada um

### Cores (amostragem de pixel)

| Uso | Cor | Contraste sobre o fundo |
| --- | --- | --- |
| Fundo da página | `#f9f9f9` | — |
| Cabeçalho e superfícies | `#ffffff` | — |
| Texto principal | `#0f0f0f` | 18,21:1 |
| Texto secundário | `#606060` | 5,97:1 |
| Marca | `#cc0000` | — |
| Links | `#065fd4` | 5,55:1 |

Todas as combinações passam em AA (mínimo 4,5:1 para texto normal).

O design é em **modo claro** — confirmado pela amostragem: cabeçalho branco,
fundo `#f9f9f9` e player preto.

### Proporções (medição das regiões)

| Medida | Design | Implementação |
| --- | --- | --- |
| Miniatura / player | 0,1675 | **0,1621** |
| Lateral / player | 0,3972 | **0,3841** |

As medidas foram tiradas medindo as regiões do design no screenshot: player com
1576px de largura, miniaturas com 264px e lateral com 626px.

### Assets

Os 10 arquivos de imagem são os originais do design, baixados das URLs de imagem
do arquivo no Figma. O design não tem avatar nos cards laterais — conferi por
amostragem de pixel e há apenas texto sobre o fundo, então não incluí esse
elemento.

---

## Acessibilidade

| Recurso | Motivo |
| --- | --- |
| **Skip link** | Pular o cabeçalho e ir ao conteúdo pelo teclado |
| **`:focus-visible`** | Contorno de 2px azul em todos os controles |
| **`aria-label`** | Todos os botões só com ícone têm nome acessível |
| **`role="search"`** | Identifica a região de busca |
| **`<label>` oculto** | A barra de busca tem rótulo, visível só para leitor de tela |
| **`role="progressbar"`** | A barra de progresso tem valor acessível |
| **`aria-labelledby`** | Seções e barra lateral nomeadas pelos próprios títulos |
| **`alt` nas imagens** | Descritivo no vídeo; vazio nos elementos decorativos |
| **`tabindex="-1"`** | O skip link move o foco de verdade, não só rola a página |
| **`prefers-reduced-motion`** | Respeita quem desativou animações |
| **`forced-colors`** | Suporte ao alto contraste do Windows |

---

## Verificações executadas

### axe-core — WCAG 2.0/2.1 A + AA + best-practice

| Violações | Verificações aprovadas |
| --- | --- |
| **0** | **46** |

### Flexbox — containers verificados no navegador

| Container | display | direction | grow | shrink | basis | wrap |
| --- | --- | --- | --- | --- | --- | --- |
| `.cabecalho` | flex | row | 0 | 1 | auto | nowrap |
| `.cabecalho__centro` | flex | row | **1** | 1 | auto | nowrap |
| `.busca` | flex | row | **1** | 1 | auto | nowrap |
| `.pagina` | flex | row | 0 | 1 | auto | nowrap |
| `.principal` | — | — | **1** | 1 | auto | — |
| `.lateral` | — | — | 0 | **0** | **424px** | — |
| `.barra-video` | flex | row | 0 | 1 | auto | **wrap** |
| `.acoes` | flex | row | 0 | 1 | auto | **wrap** |
| `.card` | flex | row | 0 | 1 | auto | nowrap |
| `.card__info` | flex | **column** | **1** | 1 | auto | nowrap |
| `.lista-videos` | flex | **column** | 0 | 1 | auto | nowrap |
| `.player__barra` | flex | row | 0 | 1 | auto | nowrap |

### Responsividade

| Largura | Overflow | Direção do flex | Lateral | Miniatura | Busca |
| --- | --- | --- | --- | --- | --- |
| 1600px | não | row | ao lado | 179px | visível |
| 1440px | não | row | ao lado | 179px | visível |
| 1200px | não | row | ao lado | 140px | visível |
| 1000px | não | **column** | abaixo | 220px | visível |
| 768px | não | **column** | abaixo | 220px | visível |
| 480px | não | **column** | abaixo | 100% | oculta |
| 360px | não | **column** | abaixo | 100% | oculta |

### Teclado

| Teste | Resultado |
| --- | --- |
| Skip link é o primeiro elemento focável | ✅ aparece em `x=0` |
| Enter move o foco para o conteúdo | ✅ foco no `#conteudo` |
| Foco visível em botões, campos e cards | ✅ 2px sólido |
| Todas as imagens carregam | ✅ 10 de 10 |
| Erros de JavaScript | nenhum |
| Requisições falhadas | nenhuma |

---

## Como visualizar

```bash
xdg-open index.html   # Linux
```

Não precisa de servidor local nem de serviço externo: os ícones são SVG
embutidos no HTML (sem Font Awesome) e as imagens são arquivos do projeto.

---

## Sobre a identidade visual

O desafio sugere dar uma identidade própria ao projeto. As cores e medidas estão
todas em variáveis CSS no bloco `:root`, então trocar a identidade é alterar um
único bloco:

```css
:root {
    --cor-fundo: #f9f9f9;
    --cor-superficie: #ffffff;
    --cor-texto: #0f0f0f;
    --cor-texto-suave: #606060;
    --cor-vermelha: #cc0000;
    --cor-azul: #065fd4;
    --largura-lateral: 424px;
    --largura-thumb: 179px;
}
```

Esta versão mantém a identidade **do design do Figma** (modo claro) para que a
fidelidade ao protótipo possa ser conferida.

---

## Referências

- [DIO](https://www.dio.me/)
- [Protótipo no Figma](https://www.figma.com/design/lrRWUZPKnqMDZrSDJmZxUS/Desafio-de-Flexbox---DIO)
- [MDN — Flexbox](https://developer.mozilla.org/pt-BR/docs/Web/CSS/CSS_flexible_box_layout)
- [MDN — `flex`](https://developer.mozilla.org/pt-BR/docs/Web/CSS/flex)
- [MDN — `min-width` em itens flex](https://developer.mozilla.org/pt-BR/docs/Web/CSS/min-width)
- [Complete Guide to Flexbox (CSS-Tricks)](https://css-tricks.com/snippets/css/a-guide-to-flexbox/)

---

Feito durante a Trilha de CSS da DIO — Desafio 02.
