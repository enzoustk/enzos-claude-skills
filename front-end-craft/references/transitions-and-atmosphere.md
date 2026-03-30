# Transições, Atmosfera, Performance e Degradação

Este arquivo cobre transições cinematográficas entre seções (clip-path, sticky, scale, horizontal scroll), camadas atmosféricas (grain, gradient orbs, vignette), performance, degradação mobile, e anti-padrões imersivos.

## Table of Contents
1. [Transições Cinematográficas entre Seções](#transicoes)
2. [Camadas Atmosféricas (Atmospheric Overlays)](#atmosfera)
3. [Performance — O Limite da Imersão](#performance)
4. [Degradação Elegante (Mobile e Low-End)](#degradacao)
5. [Anti-Padrões Imersivos](#anti-padroes)

---

<a id="transicoes"></a>
## 1. Transições Cinematográficas entre Seções

### Clip-path reveals

Em vez de uma seção simplesmente aparecer, ela se REVELA através de uma máscara:

```tsx
// Seção revelada por circle que expande do centro
<section style={{
  clipPath: `circle(${progress * 150}% at 50% 50%)`,
}}>

// Seção revelada por diagonal
<section style={{
  clipPath: `polygon(0 0, ${progress * 100}% 0, ${progress * 100 - 15}% 100%, 0 100%)`,
}}>

// Seção revelada de baixo para cima
<section style={{
  clipPath: `inset(${(1 - progress) * 100}% 0 0 0)`,
}}>
```

### Sticky section transitions

Uma seção "gruda" enquanto a próxima scroll por cima dela — ou vice-versa:

```css
.sticky-section {
  position: sticky;
  top: 0;
  height: 100vh;
}

.overlay-section {
  position: relative;
  z-index: 1;
  /* This section scrolls OVER the sticky one */
}
```

### Scale transitions

A seção atual diminui (scale down) enquanto a próxima entra:

```tsx
// Current section: shrinks and fades as you scroll past
<section style={{
  transform: `scale(${1 - exitProgress * 0.1})`,
  opacity: 1 - exitProgress,
  filter: `blur(${exitProgress * 4}px)`,
}}>
```

### Horizontal scroll section

Uma seção que scrolla horizontalmente dentro do scroll vertical:

```tsx
function HorizontalScroll({ children }) {
  const containerRef = useRef<HTMLDivElement>(null);
  const { ref, progress } = useScrollProgress();

  return (
    <div ref={ref} style={{ height: '300vh' }}> {/* tall container for scroll room */}
      <div style={{
        position: 'sticky',
        top: 0,
        height: '100vh',
        overflow: 'hidden',
      }}>
        <div
          ref={containerRef}
          style={{
            display: 'flex',
            transform: `translateX(${-progress * 66}%)`, // scroll through 3 panels
            transition: 'none', // driven by scroll, no transition
            width: '300%',
            height: '100%',
          }}
        >
          {children}
        </div>
      </div>
    </div>
  );
}
```

---

<a id="atmosfera"></a>
## 2. Camadas Atmosféricas (Atmospheric Overlays)

Camadas atmosféricas são sobrepostas **ACIMA** dos fundos personalizados (ver `references/custom-backgrounds.md`). O fundo é a superfície; a atmosfera é o que flutua sobre ela. Enquanto os fundos personalizados definem a superfície de cada seção individualmente, as camadas atmosféricas são globais — cobrem a página inteira e criam uma sensação de ambiente unificado.

### Grain/noise texture

Uma textura de grain sutil sobre toda a página adiciona uma sensação "analógica" e premium:

```css
.page-grain::after {
  content: '';
  position: fixed;
  inset: 0;
  z-index: 9000;
  pointer-events: none;
  opacity: 0.03; /* muito sutil */
  background-image: url("data:image/svg+xml,%3Csvg viewBox='0 0 256 256' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='noise'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.9' numOctaves='4' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23noise)'/%3E%3C/svg%3E");
}
```

Opacidade: 0.02-0.05 (se você percebe conscientemente, está forte demais).

### Gradient orbs

Blobs de gradiente que flutuam sutilmente no background — não como decoração óbvia, mas como ambientação:

```tsx
function AmbientOrb({ color, size, position, delay }: {
  color: string; size: number; position: { x: string; y: string }; delay: number
}) {
  return (
    <div style={{
      position: 'absolute',
      left: position.x,
      top: position.y,
      width: size,
      height: size,
      borderRadius: '50%',
      background: `radial-gradient(circle, ${color} 0%, transparent 70%)`,
      filter: 'blur(80px)',
      opacity: 0.15,
      animation: `float ${8 + delay}s ease-in-out infinite`,
      animationDelay: `${delay}s`,
      pointerEvents: 'none',
    }} />
  );
}
```

```css
@keyframes float {
  0%, 100% { transform: translate(0, 0) scale(1); }
  33% { transform: translate(15px, -20px) scale(1.05); }
  66% { transform: translate(-10px, 10px) scale(0.95); }
}
```

### Vignette

Escurecimento sutil nas bordas da viewport — efeito cinematográfico que direciona atenção ao centro:

```css
.vignette::before {
  content: '';
  position: fixed;
  inset: 0;
  pointer-events: none;
  z-index: 8999;
  background: radial-gradient(
    ellipse at center,
    transparent 50%,
    rgba(0, 0, 0, 0.3) 100%
  );
}
```

---

<a id="performance"></a>
## 3. Performance — O Limite da Imersão

### Regras de performance

1. **60fps ou não faça** — se a animação não roda suave, ela prejudica mais do que ajuda. Testar no DevTools Performance tab.

2. **Propriedades composited only** — animate APENAS `transform` e `opacity`. Nunca anime `width`, `height`, `top`, `left`, `margin`, `padding`, `border`, `background-color` em elementos com scroll-driven animation.

3. **`will-change` com moderação**:
   ```css
   .will-animate { will-change: transform, opacity; }
   ```
   Aplicar APENAS em elementos que vão animar. Nunca `will-change: transform` em 50 elementos.

4. **`requestAnimationFrame` para tudo que é per-frame** — mouse tracking, scroll progress updates, Three.js render. Nunca `setInterval` ou `setTimeout` para animação.

5. **Passive event listeners** — `{ passive: true }` em scroll e touchmove:
   ```js
   window.addEventListener('scroll', handler, { passive: true });
   ```

6. **Throttle mouse events** — mousemove dispara 60+ vezes por segundo. Throttle para ≤30fps se usado para parallax:
   ```tsx
   const handleMouseMove = useCallback(
     throttle((e: MouseEvent) => {
       setMouse({ x: e.clientX, y: e.clientY });
     }, 33), // ~30fps
     []
   );
   ```

7. **Lazy-load Three.js** — se usando WebGL, importar dinamicamente:
   ```tsx
   const ThreeHero = lazy(() => import('./ThreeHero'));
   ```

### Performance budget

```
Target:
  LCP (Largest Contentful Paint): < 2.5s
  FID (First Input Delay): < 100ms
  CLS (Cumulative Layout Shift): < 0.1
  Frame rate during scroll: ≥ 55fps sustained

Red flags:
  < 30fps during scroll → kill the heaviest effect
  LCP > 4s → defer Three.js/GSAP, load after first paint
  Bundle > 300KB JS → code-split animation libraries
```

---

<a id="degradacao"></a>
## 4. Degradação Elegante (Mobile e Low-End)

A experiência imersiva no desktop NÃO precisa ser replicada pixel-by-pixel no mobile. Mobile é outra plataforma com outras capacidades.

### Estratégia de degradação em camadas

```
DESKTOP (full experience):
  ✓ Smooth scroll (Lenis)
  ✓ Mouse parallax
  ✓ 3D tilt cards
  ✓ Custom cursor
  ✓ Three.js hero
  ✓ Scroll-driven animations (scrub)
  ✓ Clip-path transitions
  ✓ Split text animations

TABLET (reduced):
  ✓ Smooth scroll (Lenis, lighter)
  ✗ Mouse parallax → gyroscope parallax (sutil)
  ✗ 3D tilt cards → press/hold tilt (or static)
  ✗ Custom cursor → não existe
  ✗ Three.js → fallback CSS gradient
  ✓ Scroll-driven animations (simplified)
  ✓ Clip-path transitions
  ✗ Split text → fade-in por bloco

MOBILE (essential):
  ✗ Smooth scroll → scroll nativo
  ✗ Mouse parallax → nenhum (ou gyro muito sutil)
  ✗ 3D tilt → toque simples
  ✗ Custom cursor → não existe
  ✗ Three.js → fallback estático ou CSS gradient
  ✓ Scroll reveals (IntersectionObserver simples)
  ✗ Clip-path transitions → fade cross
  ✗ Split text → fade-in normal

LOW-END / prefers-reduced-motion:
  ✗ Todas as animações → conteúdo estático, sem transições
```

### Detecção

```tsx
const isTouch = typeof window !== 'undefined' && window.matchMedia('(pointer: coarse)').matches;
const prefersReducedMotion = typeof window !== 'undefined' && window.matchMedia('(prefers-reduced-motion: reduce)').matches;
const isMobile = typeof window !== 'undefined' && window.innerWidth < 768;

// Use these to conditionally render effects
{!isTouch && <CustomCursor />}
{!isMobile && <ThreeHero />}
{isMobile && <StaticHeroFallback />}
```

---

<a id="anti-padroes"></a>
## 5. Anti-Padrões Imersivos

Efeitos mal implementados são PIORES que nenhum efeito. Estes são os erros de experiências imersivas mal feitas:

### ❌ Scroll Hijacking
Roubar o scroll do usuário (smooth scroll que é tão lento que impede navegação, scroll snapping forçado entre seções). O visitante deve SEMPRE sentir controle do scroll. Lenis adiciona suavidade, não remove controle.

### ❌ Loading de 10 Segundos
Tela de loading com barra de progresso antes de mostrar o conteúdo. Em 2025+, se a página não mostra conteúdo em < 3s, o visitante saiu. Carregue o hero imediatamente, carregue efeitos pesados (Three.js) em background.

### ❌ Parallax que Causa Nausea
Elementos movendo em 15+ direções diferentes com velocidades variadas. O parallax deve ter NO MÁXIMO 3 camadas de profundidade com movement sutil (5-15px). Se o visitante sente tontura, está errado.

### ❌ Efeitos que Não Funcionam em Mobile
Implementar tudo para desktop e no mobile o site está quebrado, lento, ou sem nenhum efeito. Planeje a degradação ANTES de implementar.

### ❌ Animação que Esconde Conteúdo
Se o visitante precisa esperar uma animação terminar para ler o conteúdo ou clicar num CTA, a animação está no caminho. Conteúdo e CTAs devem ser acessíveis durante e após animações.

### ❌ "Demo Reel" em vez de Landing Page
Tantos efeitos que a página parece um portfólio de motion designer em vez de um site que quer converter. A conversão ainda é o objetivo. Efeitos servem a conversão — não o contrário.

### ❌ Performance < 30fps
Se a página treme, "engasga" ou esquenta o celular, o efeito está prejudicando, não ajudando. Teste no DevTools com CPU throttle 4x.
