# Fundos Personalizados (Background Surface Treatments)

Um `background-color` sólido é ponto de partida, não resultado final. Fundos personalizados usam gradientes, texturas ou padrões sutis para criar uma superfície que parece intencional — não gerada. A regra de ouro: **se o visitante percebe conscientemente o padrão, está forte demais.** O tratamento deve ser invisível num screenshot de celular, mas sentido no desktop.

Todas as técnicas abaixo são CSS-only (gradientes e SVG inline no `background-image`). Zero HTTP requests adicionais, composited pela GPU, sem impacto em performance.

## Table of Contents
1. [Tonal Gradient](#tonal)
2. [Mesh Gradient](#mesh)
3. [Dot Grid](#dotgrid)
4. [Noise Texture](#noise)
5. [Accent Edge Glow](#edge-glow)
6. [Tabela de Decisão](#decisao)

---

<a id="tonal"></a>
## 1. Tonal Gradient (mais simples — sempre apropriado)

Um radial ou linear gradient usando 2-3 tons da cor base. Não é um gradiente visível — é uma variação tonal sutil que impede o fundo de parecer plano. Diferença de 2-5% entre os tons.

```css
/* Dark theme — shift sutil de #09090B para #0F0F12 */
.section-hero {
  background: radial-gradient(
    ellipse at 30% 20%,
    hsl(240 6% 8%) 0%,
    hsl(240 6% 4%) 70%
  );
}

/* Light theme — shift sutil de #FAFAFA para #F4F4F8 */
.section-hero-light {
  background: radial-gradient(
    ellipse at 70% 30%,
    hsl(240 10% 98%) 0%,
    hsl(240 4% 96%) 70%
  );
}
```

O ponto focal do gradiente (`at 30% 20%`) deve estar próximo de onde o conteúdo principal se concentra — nunca perfeitamente centralizado (parece genérico).

---

<a id="mesh"></a>
## 2. Mesh Gradient (campos de cor multi-ponto)

Múltiplos radial-gradients sobrepostos em opacidade muito baixa, usando o hue do accent dessaturado. Cria um campo de cor orgânico e não-repetitivo — como luz ambiente refletida.

```css
.section-bg-mesh {
  background-color: var(--bg-base);
  background-image:
    radial-gradient(at 20% 30%, hsla(var(--accent-hue), 40%, 50%, 0.06) 0%, transparent 50%),
    radial-gradient(at 80% 70%, hsla(var(--accent-hue), 30%, 30%, 0.04) 0%, transparent 50%),
    radial-gradient(at 50% 50%, hsla(var(--accent-hue), 20%, 60%, 0.03) 0%, transparent 60%);
}
```

Regras do mesh gradient:
- Opacidade entre **0.03-0.08** por ponto — nunca acima de 0.1
- Mínimo 2 pontos, máximo 4 (mais que isso vira ruído)
- Usar o hue do accent **dessaturado** (saturation 20-40%), nunca o accent puro
- Posições assimétricas (`20% 30%`, `80% 70%`) — nunca pontos simétricos ou centralizados

---

<a id="dotgrid"></a>
## 3. Dot Grid (padrão estrutural, feel editorial)

Um padrão repetitivo de pontos minúsculos via CSS. Funciona bem em seções que precisam de uma sensação "técnica" ou "estruturada" — seções de processo, argumentos, pricing.

```css
.section-dotgrid {
  background-color: var(--bg-base);
  background-image: radial-gradient(
    circle,
    rgba(var(--text-tertiary-rgb), 0.07) 0.5px,
    transparent 0.5px
  );
  background-size: 24px 24px;
}
```

Regras do dot grid:
- Tamanho do ponto: **0.5-1px** (menor que 0.5px desaparece em telas normais)
- Opacidade dos pontos: **0.05-0.10** (controlada via rgba na cor)
- Grid size: **20-32px** (menor fica denso demais, maior fica esparso)
- Funciona melhor em dark themes onde os pontos são claros sobre fundo escuro
- **Não combine** com grain overlay na mesma seção — os padrões competem

---

<a id="noise"></a>
## 4. Noise Texture como Background (textura por seção)

Em vez do grain overlay global (que cobre toda a página — ver `references/transitions-and-atmosphere.md` → "Camadas Atmosféricas"), usar SVG noise inline no `background-image` de uma seção específica. Dá textura individual sem overlay global.

```css
.section-textured {
  background-color: var(--bg-elevated);
  background-image: url("data:image/svg+xml,%3Csvg viewBox='0 0 256 256' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='noise'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.9' numOctaves='4' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23noise)'/%3E%3C/svg%3E");
  background-blend-mode: overlay;
  background-size: 200px 200px;
}
```

Diferença do grain overlay global: o noise como background é **parte da seção**, não uma camada fixa sobre tudo. Use quando quer dar textura a uma seção específica (ex: hero, ou uma seção elevated que precisa de materialidade) sem afetar o resto.

---

<a id="edge-glow"></a>
## 5. Accent Edge Glow (cor que "escorre" de uma borda)

Um gradiente da cor accent em opacidade muito baixa posicionado em uma borda da seção (top, bottom, ou lateral). Cria a sensação de que cor "vaza" da seção adjacente ou de um elemento focal.

```css
/* Glow no topo — como se a seção anterior "derramasse" cor */
.section-with-top-glow {
  background: linear-gradient(
    to bottom,
    hsla(var(--accent-hue), 80%, 60%, 0.05) 0%,
    var(--bg-base) 30%
  );
}

/* Glow lateral — focaliza atenção no conteúdo */
.section-with-side-glow {
  background: radial-gradient(
    ellipse at 0% 50%,
    hsla(var(--accent-hue), 70%, 50%, 0.04) 0%,
    var(--bg-base) 40%
  );
}
```

Regras do accent edge glow:
- Opacidade do accent: **0.03-0.06** — se a cor é reconhecível, está forte demais
- O glow deve ocupar no máximo **30-40%** da área da seção, fading para base
- Funciona especialmente bem no **Final CTA** (aquece a seção e direciona atenção) e no **topo de seções** que seguem uma seção de destaque
- **Nunca** use o accent em opacidade alta — isso viola a regra dos 15%

---

<a id="decisao"></a>
## 6. Tabela de Decisão: quando usar cada técnica

| Tipo de seção | Tratamento recomendado | Por quê |
|--------------|----------------------|---------|
| **Hero** | Tonal gradient + mesh gradient | Primeira impressão — merece a maior complexidade |
| **Credibility bar** | Base limpa ou tonal gradient mínimo | Seção fina e discreta — fundo limpo mantém foco no conteúdo |
| **Process / How it works** | Dot grid ou base limpa | Conteúdo estrutural se beneficia do feel técnico do grid |
| **Arguments / Why us** | Tonal gradient | Seção de conteúdo denso — fundo quieto que não compete |
| **Numbers / Stats** | Base limpa ou accent edge glow sutil | Os números são o destaque — fundo não deve competir |
| **FAQ** | Base limpa | Seção de leitura intensa — fundo calmo é obrigatório |
| **Final CTA** | Accent edge glow + tonal gradient | O glow aquece a seção e direciona o olhar ao CTA |

**Princípio geral**: seções de leitura intensa (FAQ, argumentos longos) usam fundos mais limpos. Seções de impacto visual (hero, CTA) usam tratamentos mais ricos. Nunca todas as seções com o mesmo tratamento — o contraste entre fundo tratado e fundo limpo é parte do ritmo visual.
