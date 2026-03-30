# Elementos Visuais Decorativos

Elementos decorativos são a terceira camada visual: o fundo personalizado (`references/custom-backgrounds.md`) é a superfície, a atmosfera (`references/transitions-and-atmosphere.md` → "Camadas Atmosféricas") flutua globalmente, e os elementos decorativos são **detalhes posicionados intencionalmente** que reforçam hierarquia e composição. Não são conteúdo, não são atmosfera — são acabamento artesanal.

## Table of Contents
1. [Accent Line](#accent-line)
2. [Gradient Border](#gradient-border)
3. [Glow Effect](#glow-effect)
4. [Decorative Dots / Circles](#dots)
5. [Geometric Background Shapes](#shapes)
6. [Gradient Text Accent](#gradient-text)

---

<a id="accent-line"></a>
## 1. Accent Line

Uma linha fina em accent color posicionada como marcador visual — acima de overlines, ao lado de títulos, como elemento compositivo no hero.

```css
/* Linha acima de overline */
.overline::before {
  content: '';
  display: block;
  width: 40px;
  height: 2px;
  background: var(--accent);
  margin-bottom: 12px;
}

/* Linha ao lado de section title */
.section-title-with-line {
  display: flex;
  align-items: center;
  gap: 16px;
}
.section-title-with-line::before {
  content: '';
  width: 48px;
  height: 1px;
  background: var(--accent);
  flex-shrink: 0;
}
```

Regras: max 2-3 accent lines por página. Largura 40-80px, altura 1-2px. Se aparecem em todo título, perdem o impacto.

---

<a id="gradient-border"></a>
## 2. Gradient Border

Borda com gradiente para destacar um card ou área específica. Usa pseudo-element porque `border-image` não funciona com `border-radius`.

```css
.gradient-border-card {
  position: relative;
  border-radius: 12px;
  padding: 32px;
  background: var(--bg-elevated);
}

.gradient-border-card::before {
  content: '';
  position: absolute;
  inset: 0;
  border-radius: inherit;
  padding: 1px; /* espessura da borda */
  background: linear-gradient(
    135deg,
    hsla(var(--accent-hue), 80%, 60%, 0.4),
    hsla(var(--accent-hue), 60%, 40%, 0.1)
  );
  -webkit-mask: linear-gradient(#fff 0 0) content-box, linear-gradient(#fff 0 0);
  -webkit-mask-composite: xor;
  mask-composite: exclude;
  pointer-events: none;
}
```

Regras: usar em NO MÁXIMO 1-2 cards por página (o card featured, o plano recomendado, a área do CTA). Se todo card tem gradient border, nenhum se destaca.

---

<a id="glow-effect"></a>
## 3. Glow Effect (Element-Level)

Diferente dos gradient orbs atmosféricos (`references/transitions-and-atmosphere.md` → "Gradient orbs"), este glow é atrelado a um elemento específico — aparece no hover ou como destaque sutil.

```css
/* Glow no hover de card */
.card-glow:hover {
  box-shadow: 0 0 40px hsla(var(--accent-hue), 70%, 50%, 0.08);
}

/* Glow sutil permanente atrás de CTA */
.cta-glow {
  box-shadow: 0 0 30px hsla(var(--accent-hue), 80%, 60%, 0.12);
}

/* Glow pulsante no CTA final */
.cta-pulse {
  animation: cta-glow-pulse 2s ease-in-out infinite;
}

@keyframes cta-glow-pulse {
  0%, 100% { box-shadow: 0 0 0 0 hsla(var(--accent-hue), 80%, 60%, 0.15); }
  50% { box-shadow: 0 0 20px 8px hsla(var(--accent-hue), 80%, 60%, 0); }
}
```

Regras: opacidade do glow 0.05-0.15. Se é visível como um halo nítido, está forte demais. O glow deve ser sentido, não visto.

---

<a id="dots"></a>
## 4. Decorative Dots / Circles

Pequenos círculos posicionados como acentos composicionais — não aleatórios, cada um tem uma razão.

```css
/* Dot compositivo — posicionado com absolute */
.decorative-dot {
  width: 6px;
  height: 6px;
  border-radius: 50%;
  background: var(--accent);
  opacity: 0.3;
  position: absolute;
}

/* Ring decorativo (circle outline) */
.decorative-ring {
  width: 24px;
  height: 24px;
  border-radius: 50%;
  border: 1px solid var(--accent);
  opacity: 0.15;
  position: absolute;
}
```

Uso: no canto de áreas do hero (como marcador de composição), ao lado de step numbers em seções de processo, em interseções de grids bento. Max 3-5 por página.

---

<a id="shapes"></a>
## 5. Geometric Background Shapes

Formas SVG sutis posicionadas atrás do conteúdo como camada de profundidade. Diferentes dos gradient orbs (que são atmosfera difusa) — estas são formas reconhecíveis em opacidade muito baixa.

```tsx
function DecorativeShape({ type, size, position, rotation = 0 }: {
  type: 'circle' | 'ring' | 'cross' | 'square';
  size: number;
  position: { x: string; y: string };
  rotation?: number;
}) {
  const shapes = {
    circle: <circle cx={size/2} cy={size/2} r={size/2} />,
    ring: <circle cx={size/2} cy={size/2} r={size/2 - 1} fill="none" strokeWidth="1" />,
    cross: <path d={`M${size/2} 0 V${size} M0 ${size/2} H${size}`} fill="none" strokeWidth="1" />,
    square: <rect width={size * 0.7} height={size * 0.7} x={size * 0.15} y={size * 0.15} fill="none" strokeWidth="1" />,
  };

  return (
    <svg
      width={size}
      height={size}
      viewBox={`0 0 ${size} ${size}`}
      style={{
        position: 'absolute',
        left: position.x,
        top: position.y,
        opacity: 0.06,
        stroke: 'var(--accent)',
        fill: type === 'circle' ? 'var(--accent)' : 'none',
        transform: `rotate(${rotation}deg)`,
        pointerEvents: 'none',
      }}
    >
      {shapes[type]}
    </svg>
  );
}
```

Regras: opacidade 0.04-0.08. Tamanho 20-60px. Posicionados em camada parallax (movem com mouse) ou estáticos. Max 3-4 shapes por página. Nunca no foreground — sempre `z-index` abaixo do conteúdo.

---

<a id="gradient-text"></a>
## 6. Gradient Text Accent

Uma palavra no headline com gradiente em vez de cor sólida. Mais impactful que accent color sólido, mas usar com extrema moderação.

```css
.gradient-word {
  background: linear-gradient(
    135deg,
    var(--accent) 0%,
    hsla(var(--accent-hue), 90%, 70%, 1) 100%
  );
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}
```

Regras: **UMA palavra por página, máximo.** Apenas no hero headline. Se mais de uma palavra tem gradiente, o efeito perde toda a potência.
