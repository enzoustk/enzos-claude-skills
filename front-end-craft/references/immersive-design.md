# Immersive Design — Profundidade, 3D e Interatividade

Este arquivo é o guia para criar landing pages que o visitante SENTE, não apenas lê. A página não é um documento — é um ambiente. O scroll não é navegação — é exploração. O cursor não é um ponteiro — é presença.

A diferença entre uma LP flat e uma LP imersiva não é "mais animação". É que cada pixel responde ao visitante. A página está viva.

## Table of Contents
1. [Filosofia: De Flat para Dimensional](#filosofia)
2. [Scroll como Narrativa](#scroll-narrativa)
3. [Profundidade e Parallax com Propósito](#parallax)
4. [3D com CSS e WebGL](#3d)
5. [Cursor como Presença](#cursor)
6. [Cards e Elementos com Profundidade](#cards-3d)
7. [Transições Cinematográficas entre Seções](#transicoes)
8. [Texto que Vive](#texto-vivo)
9. [Atmosfera, Fundos, Ambientação e Elementos Decorativos](#atmosfera)
10. [Smooth Scroll (Lenis)](#smooth-scroll)
11. [Stack Técnico Imersivo](#stack)
12. [Performance — O Limite da Imersão](#performance)
13. [Degradação Elegante (Mobile e Low-End)](#degradacao)
14. [Anti-Padrões Imersivos](#anti-padroes)

---

<a id="filosofia"></a>
## 1. Filosofia: De Flat para Dimensional

### O problema das LPs flat

Uma landing page flat mostra informação. Uma landing page imersiva cria uma experiência. A diferença:

```
FLAT (o visitante LÊ):                IMERSIVO (o visitante SENTE):
─────────────────────                  ─────────────────────────────
Scroll → seção aparece                 Scroll → o mundo se move ao redor
Hover → cor muda                       Hover → elemento responde em 3D
Texto entra → fade up                  Texto entra → emerge da profundidade
Seções empilhadas                      Seções se transformam uma na outra
Fundo estático                         Fundo respira e reage
Cursor é um ponteiro                   Cursor é uma presença que influencia
```

### Os 3 eixos de imersão

Toda LP imersiva combina estes três eixos. Não precisa usar os três no máximo — mas precisa de pelo menos dois:

**Eixo Z (Profundidade)**: Elementos existem em camadas. O fundo se move diferente do conteúdo. Cards têm perspectiva. Texto emerge de trás para a frente. O visitante sente que está olhando PARA DENTRO da tela, não para uma superfície plana.

**Eixo Tempo (Scroll-driven)**: O scroll não é "pular entre seções" — é uma timeline contínua. Elementos se transformam progressivamente. A opacidade, posição, escala e rotação mudam proporcionalmente ao scroll. O visitante controla o ritmo da narrativa.

**Eixo Reação (Input-driven)**: A página responde ao visitante. O cursor influencia partículas, inclina cards, move luz. O toque arrasta elementos. O gyroscópio do celular orienta o parallax. O visitante sente que a página SABE que ele está ali.

### A regra de ouro da imersão

**Todo efeito deve recompensar uma ação do visitante.** Scrollou → algo se revelou. Moveu o mouse → algo reagiu. Hover num card → ele se abriu para você. Se o efeito acontece sozinho sem nenhuma ação do visitante, ele é decoração passiva — e decoração passiva é o primeiro sinal de experiência superficial.

Exceção: ambientação sutil no fundo (grain, gradientes que respiram, partículas lentas) pode ser passiva porque cria atmosfera sem competir com o conteúdo.

---

<a id="scroll-narrativa"></a>
## 2. Scroll como Narrativa

### De IntersectionObserver para Scroll Progress

LPs flat usam IntersectionObserver: "quando o elemento aparecer, anima". Isso cria um efeito binário — invisível ou visível, off ou on. 

LPs imersivas usam **scroll progress**: a animação é proporcional à posição do scroll. O elemento não "aparece" — ele se transforma ENQUANTO o visitante scrolla.

### Hook: useScrollProgress

```tsx
import { useRef, useState, useEffect } from 'react';

export function useScrollProgress(options: { 
  start?: string;   // e.g., "top bottom" = quando o topo do elemento atinge o bottom da viewport
  end?: string;      // e.g., "bottom top" = quando o bottom do elemento atinge o top da viewport
} = {}) {
  const ref = useRef<HTMLElement>(null);
  const [progress, setProgress] = useState(0); // 0 to 1

  useEffect(() => {
    const element = ref.current;
    if (!element) return;

    const update = () => {
      const rect = element.getBoundingClientRect();
      const windowHeight = window.innerHeight;
      
      // Progress: 0 when element enters viewport, 1 when it leaves
      const start = rect.top - windowHeight;
      const end = rect.bottom;
      const total = end - start;
      const current = -start;
      
      setProgress(Math.min(Math.max(current / total, 0), 1));
    };

    window.addEventListener('scroll', update, { passive: true });
    update();
    return () => window.removeEventListener('scroll', update);
  }, []);

  return { ref, progress };
}
```

### Exemplos de scroll-driven animation

**Texto que se revela por máscara (clip-path)**:
```tsx
function RevealHeadline() {
  const { ref, progress } = useScrollProgress();
  
  return (
    <h2
      ref={ref}
      style={{
        clipPath: `inset(0 ${100 - progress * 100}% 0 0)`,
        transform: `translateX(${(1 - progress) * 40}px)`,
      }}
    >
      Seu ouro tem valor.
    </h2>
  );
}
```

**Seção que faz zoom-in ao scrollar**:
```tsx
<section
  ref={ref}
  style={{
    transform: `scale(${0.9 + progress * 0.1})`,
    opacity: Math.min(progress * 2, 1),
  }}
/>
```

**Parallax entre camadas (foreground e background)**:
```tsx
<div style={{ transform: `translateY(${progress * -80}px)` }}>  {/* background: move lento */}
<div style={{ transform: `translateY(${progress * -20}px)` }}>  {/* foreground: move rápido */}
```

### CSS Scroll-Driven Animations (nativo, sem JS)

Browsers modernos suportam `animation-timeline: scroll()` — animação CSS pura vinculada ao scroll:

```css
@keyframes reveal {
  from { opacity: 0; transform: translateY(60px) scale(0.95); }
  to   { opacity: 1; transform: translateY(0) scale(1); }
}

.scroll-reveal {
  animation: reveal linear both;
  animation-timeline: scroll();
  animation-range: entry 0% entry 60%;
}
```

Use como progressive enhancement — funciona em Chrome/Edge, fallback para IntersectionObserver em Safari/Firefox.

---

<a id="parallax"></a>
## 3. Profundidade e Parallax com Propósito

### O parallax certo vs. o parallax errado

**Errado**: Imagem de fundo se move em velocidade diferente do conteúdo. É o parallax dos anos 2010 — clichê, causa motion sickness, não comunica nada.

**Certo**: Elementos DENTRO da composição existem em camadas de profundidade. O título está numa camada, o subtítulo em outra, o fundo em outra. Quando o visitante scrolla, cada camada se move em velocidade diferente — criando profundidade real, como olhar através de uma janela.

### Implementação com CSS perspective

```css
.parallax-container {
  perspective: 1000px;
  perspective-origin: center;
  height: 100vh;
  overflow-x: hidden;
  overflow-y: auto;
}

.layer-back {
  transform: translateZ(-200px) scale(1.2); /* mais longe = menor, compensar com scale */
}

.layer-mid {
  transform: translateZ(-100px) scale(1.1);
}

.layer-front {
  transform: translateZ(0); /* plano normal */
}
```

### Parallax com mouse (não só scroll)

O visitante move o mouse e as camadas respondem sutilmente — como um efeito de holografia:

```tsx
function ParallaxLayers() {
  const [mouse, setMouse] = useState({ x: 0, y: 0 });

  const handleMouseMove = (e: React.MouseEvent) => {
    const x = (e.clientX / window.innerWidth - 0.5) * 2;  // -1 to 1
    const y = (e.clientY / window.innerHeight - 0.5) * 2;
    setMouse({ x, y });
  };

  return (
    <div onMouseMove={handleMouseMove}>
      {/* Camada de fundo: move pouco */}
      <div style={{ transform: `translate(${mouse.x * 8}px, ${mouse.y * 8}px)` }}>
        {/* background elements */}
      </div>
      
      {/* Camada de conteúdo: move menos ainda */}
      <div style={{ transform: `translate(${mouse.x * 3}px, ${mouse.y * 3}px)` }}>
        {/* headline, subtitle */}
      </div>
      
      {/* Camada de frente: estática ou move na direção oposta */}
      <div style={{ transform: `translate(${mouse.x * -2}px, ${mouse.y * -2}px)` }}>
        {/* accent elements, glow */}
      </div>
    </div>
  );
}
```

**Intensidade**: Camadas de fundo movem 5-15px. Conteúdo move 2-5px. Qualquer coisa acima de 20px fica nauseante. A sutileza é o que torna premium — se o visitante PERCEBE conscientemente o parallax, está forte demais.

---

<a id="3d"></a>
## 4. 3D com CSS e WebGL

### CSS 3D Transforms — quando usar

CSS 3D é leve, funciona em todo browser, e cria efeitos de profundidade convincentes:

**Tilt em hover (cards)**:
```css
.card-3d {
  transform-style: preserve-3d;
  transition: transform 0.4s cubic-bezier(0.03, 0.98, 0.52, 0.99);
}

.card-3d:hover {
  transform: perspective(800px) rotateX(4deg) rotateY(-4deg) translateZ(10px);
}
```

**Texto com profundidade (text-shadow como Z-axis)**:
```css
.deep-text {
  text-shadow:
    0 1px 0 rgba(0,0,0,0.1),
    0 2px 0 rgba(0,0,0,0.08),
    0 4px 0 rgba(0,0,0,0.06),
    0 8px 8px rgba(0,0,0,0.1),
    0 16px 16px rgba(0,0,0,0.05);
}
```

**Seção que "gira" ao scrollar**:
```tsx
<section style={{
  transform: `perspective(1200px) rotateX(${(1 - progress) * 8}deg)`,
  transformOrigin: 'bottom center',
}}>
```

### Three.js / WebGL — quando usar

Use Three.js SOMENTE no hero ou como background de uma seção, quando:
- O brand é tech/futurista/premium e o visual 3D comunica a identidade
- Há um elemento 3D concreto que faz sentido (produto rotativo, partículas que formam logo, terreno abstrato)
- O cliente quer um hero "wow factor" que nenhum CSS consegue

**Setup minimalista Three.js para hero**:

```tsx
import { useRef, useEffect } from 'react';
import * as THREE from 'three';

function HeroBackground() {
  const containerRef = useRef<HTMLDivElement>(null);

  useEffect(() => {
    const container = containerRef.current;
    if (!container) return;

    const scene = new THREE.Scene();
    const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
    const renderer = new THREE.WebGLRenderer({ alpha: true, antialias: true });
    
    renderer.setSize(window.innerWidth, window.innerHeight);
    renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2)); // cap at 2x for performance
    container.appendChild(renderer.domElement);

    // --- YOUR 3D SCENE HERE ---
    // Geometry, materials, lights, etc.

    camera.position.z = 5;

    // Mouse interaction
    const mouse = new THREE.Vector2();
    const onMouseMove = (e: MouseEvent) => {
      mouse.x = (e.clientX / window.innerWidth) * 2 - 1;
      mouse.y = -(e.clientY / window.innerHeight) * 2 + 1;
    };
    window.addEventListener('mousemove', onMouseMove);

    // Render loop
    const animate = () => {
      requestAnimationFrame(animate);
      // Animate based on mouse, scroll, time
      renderer.render(scene, camera);
    };
    animate();

    // Resize handler
    const onResize = () => {
      camera.aspect = window.innerWidth / window.innerHeight;
      camera.updateProjectionMatrix();
      renderer.setSize(window.innerWidth, window.innerHeight);
    };
    window.addEventListener('resize', onResize);

    return () => {
      window.removeEventListener('mousemove', onMouseMove);
      window.removeEventListener('resize', onResize);
      renderer.dispose();
      container.removeChild(renderer.domElement);
    };
  }, []);

  return (
    <div 
      ref={containerRef} 
      style={{ position: 'absolute', inset: 0, zIndex: 0 }}
    />
  );
}
```

### Cenas 3D que funcionam para LPs

**Partículas flutuantes** — pontos que flutuam lentamente, reagem ao cursor, formam shapes abstratas. Funciona para: tech, cripto, SaaS, agências.

**Gradiente mesh 3D** — uma superfície ondulada com gradiente que se deforma suavemente. Funciona para: qualquer brand premium, é elegante sem ser óbvio.

**Objeto product showcase** — o produto 3D do cliente (joia, dispositivo, embalagem) flutuando, rotacionando lentamente, reagindo ao mouse. Funciona para: e-commerce premium, joalheria, tech hardware.

**Terreno abstrato** — mesh wireframe ou sólido que ondula como água ou terreno. Funciona para: fintech, imobiliária, marcas "sólidas".

### Regras de uso do Three.js

1. **Apenas UMA cena 3D por página** — geralmente no hero. Múltiplas cenas WebGL matam a performance.
2. **Canvas com `alpha: true`** — fundo transparente, o 3D se integra ao layout.
3. **`setPixelRatio(Math.min(devicePixelRatio, 2))`** — cap em 2x, telas retina em 3x destroem performance.
4. **Dispose on unmount** — limpar geometrias, materiais, texturas. Memory leak é real.
5. **Fallback para mobile** — detectar device com `navigator.maxTouchPoints > 0` ou matchMedia. Em mobile, simplificar drasticamente ou substituir por CSS.

---

<a id="cursor"></a>
## 5. Cursor como Presença

### Custom cursor

O cursor default é invisible — ninguém percebe. Um cursor customizado faz o visitante sentir que está "dentro" do site:

```tsx
function CustomCursor() {
  const [pos, setPos] = useState({ x: 0, y: 0 });
  const [isHovering, setIsHovering] = useState(false);

  useEffect(() => {
    const move = (e: MouseEvent) => setPos({ x: e.clientX, y: e.clientY });
    window.addEventListener('mousemove', move);

    // Detect hover on interactive elements
    const interactives = document.querySelectorAll('a, button, [data-cursor-hover]');
    interactives.forEach(el => {
      el.addEventListener('mouseenter', () => setIsHovering(true));
      el.addEventListener('mouseleave', () => setIsHovering(false));
    });

    return () => window.removeEventListener('mousemove', move);
  }, []);

  return (
    <>
      {/* Dot (follows instantly) */}
      <div
        style={{
          position: 'fixed',
          left: pos.x - 4,
          top: pos.y - 4,
          width: 8,
          height: 8,
          borderRadius: '50%',
          background: 'var(--color-accent)',
          pointerEvents: 'none',
          zIndex: 99999,
          transition: 'transform 0.15s ease-out',
          transform: isHovering ? 'scale(0)' : 'scale(1)',
        }}
      />
      {/* Ring (follows with delay) */}
      <div
        style={{
          position: 'fixed',
          left: pos.x - 20,
          top: pos.y - 20,
          width: 40,
          height: 40,
          borderRadius: '50%',
          border: `1px solid var(--color-accent)`,
          opacity: 0.5,
          pointerEvents: 'none',
          zIndex: 99998,
          transition: 'all 0.2s cubic-bezier(0.16, 1, 0.3, 1)',
          transform: isHovering ? 'scale(2)' : 'scale(1)',
        }}
      />
    </>
  );
}
```

**Regras**:
- O cursor customizado é ADICIONAL ao cursor do browser (não esconda o cursor nativo com `cursor: none` — acessibilidade)
- Ou: esconda o nativo SOMENTE em desktop, e desabilite o customizado em touch devices
- O ring deve ter `transition` com delay suave para criar o efeito de "elasticidade"
- No hover sobre links/botões, o ring expande e o dot desaparece
- NUNCA no mobile (não existe cursor em touch)

### Elementos que reagem ao cursor

Além do cursor customizado, elementos individuais podem reagir à proximidade do mouse:

**Magnetic buttons** — o botão se move sutilmente em direção ao cursor quando está próximo:

```tsx
function MagneticButton({ children, ...props }) {
  const ref = useRef<HTMLButtonElement>(null);
  const [offset, setOffset] = useState({ x: 0, y: 0 });

  const handleMouseMove = (e: React.MouseEvent) => {
    const btn = ref.current;
    if (!btn) return;
    const rect = btn.getBoundingClientRect();
    const x = e.clientX - rect.left - rect.width / 2;
    const y = e.clientY - rect.top - rect.height / 2;
    setOffset({ x: x * 0.3, y: y * 0.3 }); // 30% of distance
  };

  const handleMouseLeave = () => setOffset({ x: 0, y: 0 });

  return (
    <button
      ref={ref}
      onMouseMove={handleMouseMove}
      onMouseLeave={handleMouseLeave}
      style={{
        transform: `translate(${offset.x}px, ${offset.y}px)`,
        transition: 'transform 0.3s cubic-bezier(0.16, 1, 0.3, 1)',
      }}
      {...props}
    >
      {children}
    </button>
  );
}
```

---

<a id="cards-3d"></a>
## 6. Cards e Elementos com Profundidade

### O 3D tilt card

Cards que inclinam em 3D conforme o cursor se move sobre eles — o efeito mais impactante com o melhor custo-benefício:

```tsx
function TiltCard({ children }) {
  const ref = useRef<HTMLDivElement>(null);
  const [transform, setTransform] = useState('');
  const [glare, setGlare] = useState({ x: 50, y: 50, opacity: 0 });

  const handleMouseMove = (e: React.MouseEvent) => {
    const card = ref.current;
    if (!card) return;
    const rect = card.getBoundingClientRect();
    const x = (e.clientX - rect.left) / rect.width;   // 0 to 1
    const y = (e.clientY - rect.top) / rect.height;

    const rotateX = (y - 0.5) * -12;  // -6 to 6 degrees
    const rotateY = (x - 0.5) * 12;

    setTransform(
      `perspective(800px) rotateX(${rotateX}deg) rotateY(${rotateY}deg) translateZ(20px)`
    );
    setGlare({ x: x * 100, y: y * 100, opacity: 0.15 });
  };

  const handleMouseLeave = () => {
    setTransform('perspective(800px) rotateX(0) rotateY(0) translateZ(0)');
    setGlare({ x: 50, y: 50, opacity: 0 });
  };

  return (
    <div
      ref={ref}
      onMouseMove={handleMouseMove}
      onMouseLeave={handleMouseLeave}
      style={{
        transform,
        transition: 'transform 0.4s cubic-bezier(0.03, 0.98, 0.52, 0.99)',
        transformStyle: 'preserve-3d',
        position: 'relative',
        overflow: 'hidden',
      }}
    >
      {children}
      {/* Glare effect */}
      <div
        style={{
          position: 'absolute',
          inset: 0,
          background: `radial-gradient(circle at ${glare.x}% ${glare.y}%, rgba(255,255,255,${glare.opacity}), transparent 60%)`,
          pointerEvents: 'none',
          transition: 'opacity 0.3s ease',
        }}
      />
    </div>
  );
}
```

### Depth layers dentro de cards

Elementos DENTRO do card podem existir em Z-depths diferentes — criando profundidade interna:

```css
.card-content {
  transform-style: preserve-3d;
}

.card-icon {
  transform: translateZ(40px); /* flutua acima do card */
}

.card-title {
  transform: translateZ(25px);
}

.card-description {
  transform: translateZ(10px);
}

.card-bg-pattern {
  transform: translateZ(-10px); /* afunda atrás */
}
```

Quando o card inclina, esses elementos se movem em velocidades diferentes — criando um efeito de "caixa com camadas" que é extremamente premium.

---

<a id="transicoes"></a>
## 7. Transições Cinematográficas entre Seções

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

<a id="texto-vivo"></a>
## 8. Texto que Vive

### Split text animation

Dividir texto em caracteres ou palavras e animar individualmente:

```tsx
function SplitText({ text, progress }: { text: string; progress: number }) {
  const words = text.split(' ');
  
  return (
    <span>
      {words.map((word, i) => {
        const wordProgress = Math.min(
          Math.max((progress * words.length - i) / 1, 0),
          1
        );
        return (
          <span
            key={i}
            style={{
              display: 'inline-block',
              opacity: wordProgress,
              transform: `translateY(${(1 - wordProgress) * 20}px)`,
              transition: 'none',
              marginRight: '0.3em',
            }}
          >
            {word}
          </span>
        );
      })}
    </span>
  );
}
```

### Text gradient reveal

O texto começa cinza e a cor "preenche" da esquerda para a direita conforme o scroll:

```tsx
<h2 style={{
  background: `linear-gradient(90deg, 
    var(--color-text-primary) ${progress * 100}%, 
    var(--color-text-tertiary) ${progress * 100}%
  )`,
  WebkitBackgroundClip: 'text',
  WebkitTextFillColor: 'transparent',
}}>
```

### Counter with spring easing

Números que "saltam" para o valor final com easing orgânico:

```tsx
function SpringCounter({ target, duration = 1200 }) {
  const [value, setValue] = useState(0);
  const { ref, isInView } = useInView();

  useEffect(() => {
    if (!isInView) return;
    const start = performance.now();
    
    const animate = (now: number) => {
      const elapsed = now - start;
      const rawProgress = Math.min(elapsed / duration, 1);
      // Spring-like easing: overshoot slightly, then settle
      const progress = rawProgress < 0.8
        ? 1 - Math.pow(1 - rawProgress / 0.8, 3)  // ease-out for first 80%
        : 1 + Math.sin((rawProgress - 0.8) * Math.PI / 0.2) * 0.03; // tiny overshoot

      setValue(Math.round(progress * target));
      if (rawProgress < 1) requestAnimationFrame(animate);
      else setValue(target);
    };
    
    requestAnimationFrame(animate);
  }, [isInView, target, duration]);

  return <span ref={ref}>{value.toLocaleString('pt-BR')}</span>;
}
```

---

<a id="atmosfera"></a>
## 9. Atmosfera, Fundos e Ambientação

A atmosfera de uma página tem duas camadas distintas: a **superfície do fundo** (o que o background da seção É) e as **camadas atmosféricas** (o que flutua ACIMA dele — grain, orbs, vignette). Ambas trabalham juntas, mas são implementadas separadamente.

### 9.1 Fundos Personalizados (Background Surface Treatments)

Um `background-color` sólido é ponto de partida, não resultado final. Fundos personalizados usam gradientes, texturas ou padrões sutis para criar uma superfície que parece intencional — não gerada. A regra de ouro: **se o visitante percebe conscientemente o padrão, está forte demais.** O tratamento deve ser invisível num screenshot de celular, mas sentido no desktop.

Todas as técnicas abaixo são CSS-only (gradientes e SVG inline no `background-image`). Zero HTTP requests adicionais, composited pela GPU, sem impacto em performance.

#### Técnica 1: Tonal Gradient (mais simples — sempre apropriado)

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

#### Técnica 2: Mesh Gradient (campos de cor multi-ponto)

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

#### Técnica 3: Dot Grid (padrão estrutural, feel editorial)

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

#### Técnica 4: Noise Texture como Background (textura por seção)

Em vez do grain overlay global (que cobre toda a página), usar SVG noise inline no `background-image` de uma seção específica. Dá textura individual sem overlay global.

```css
.section-textured {
  background-color: var(--bg-elevated);
  background-image: url("data:image/svg+xml,%3Csvg viewBox='0 0 256 256' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='noise'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.9' numOctaves='4' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23noise)'/%3E%3C/svg%3E");
  background-blend-mode: overlay;
  background-size: 200px 200px;
}
```

Diferença do grain overlay global: o noise como background é **parte da seção**, não uma camada fixa sobre tudo. Use quando quer dar textura a uma seção específica (ex: hero, ou uma seção elevated que precisa de materialidade) sem afetar o resto.

#### Técnica 5: Accent Edge Glow (cor que "escorre" de uma borda)

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

#### Tabela de decisão: quando usar cada técnica

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

### 9.2 Camadas Atmosféricas (Atmospheric Overlays)

Camadas atmosféricas são sobrepostas **ACIMA** dos fundos personalizados. O fundo é a superfície; a atmosfera é o que flutua sobre ela. Enquanto os fundos personalizados (9.1) definem a superfície de cada seção individualmente, as camadas atmosféricas são globais — cobrem a página inteira e criam uma sensação de ambiente unificado.

#### Grain/noise texture

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

#### Gradient orbs

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

#### Vignette

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

### 9.3 Elementos Visuais Decorativos (Decorative Visual Elements)

Elementos decorativos são a terceira camada visual: o fundo personalizado (9.1) é a superfície, a atmosfera (9.2) flutua globalmente, e os elementos decorativos são **detalhes posicionados intencionalmente** que reforçam hierarquia e composição. Não são conteúdo, não são atmosfera — são acabamento artesanal.

#### Accent Line

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

#### Gradient Border

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

#### Glow Effect (Element-Level)

Diferente dos gradient orbs atmosféricos (9.2), este glow é atrelado a um elemento específico — aparece no hover ou como destaque sutil.

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

#### Decorative Dots / Circles

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

#### Geometric Background Shapes

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

#### Gradient Text Accent

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

---

<a id="smooth-scroll"></a>
## 10. Smooth Scroll (Lenis)

O scroll nativo do browser é "mecânico" — para instantaneamente quando o dedo sai. Smooth scroll com Lenis cria momentum e desaceleração orgânica.

### Setup

```bash
npm install lenis
```

```tsx
// App.tsx or layout component
import Lenis from 'lenis';
import { useEffect } from 'react';

function App() {
  useEffect(() => {
    const lenis = new Lenis({
      duration: 1.2,          // scroll duration
      easing: (t) => Math.min(1, 1.001 - Math.pow(2, -10 * t)), // ease-out expo
      smoothWheel: true,
      touchMultiplier: 1.5,   // mobile touch sensitivity
    });

    function raf(time: number) {
      lenis.raf(time);
      requestAnimationFrame(raf);
    }
    requestAnimationFrame(raf);

    return () => lenis.destroy();
  }, []);

  return <main>{/* page content */}</main>;
}
```

### Lenis + scroll progress

```tsx
// Connect Lenis to scroll-driven animations
lenis.on('scroll', ({ progress, scroll }) => {
  // Use these values in your animation system
  document.documentElement.style.setProperty('--scroll', scroll.toString());
  document.documentElement.style.setProperty('--scroll-progress', progress.toString());
});
```

### Quando NÃO usar Lenis

- Em páginas com muitos formulários (smooth scroll interfere com inputs)
- Se o usuário tiver `prefers-reduced-motion: reduce` — respeitar e desabilitar
- Em mobile se causar conflito com scroll nativo do browser (testar!)

---

<a id="stack"></a>
## 11. Stack Técnico Imersivo

O stack para páginas imersivas é diferente do stack flat:

```
CORE:
  React 19 + Vite + Tailwind CSS v4         (base — mesmo de antes)
  Lenis                                       (smooth scroll)
  
ANIMAÇÃO (escolher 1):
  GSAP + ScrollTrigger                        (opção robusta — melhor scroll-driven)
  Framer Motion                               (opção React-native — melhor integração)
  CSS Scroll-Driven Animations                (opção zero-dep — progressive enhancement)

3D (opcional, apenas se necessário):
  Three.js                                    (hero 3D, product showcase)
  
ÍCONES:
  Lucide React                                (mesmo de antes)
  
FONTES:
  Google Fonts                                (mesmo de antes)
```

### GSAP vs Framer Motion vs CSS

**GSAP + ScrollTrigger**: A opção mais poderosa. Perfeita para scroll-driven animations complexas, timelines, scrub (animação proporcional ao scroll). Desvantagem: não é React-idiomatic, usa refs e DOM manipulation.

**Framer Motion**: A opção mais React-native. `useScroll()`, `useTransform()`, `motion.div` com `whileInView`. Desvantagem: menos controle fino que GSAP para scroll-driven.

**CSS Scroll-Driven**: Zero JavaScript. `animation-timeline: scroll()`. Desvantagem: suporte parcial (Chrome/Edge sim, Safari/Firefox parcial). Use como progressive enhancement.

### GSAP ScrollTrigger pattern

```tsx
import { useEffect, useRef } from 'react';
import gsap from 'gsap';
import { ScrollTrigger } from 'gsap/ScrollTrigger';

gsap.registerPlugin(ScrollTrigger);

function ImmersiveSection() {
  const sectionRef = useRef(null);
  const titleRef = useRef(null);

  useEffect(() => {
    const ctx = gsap.context(() => {
      gsap.from(titleRef.current, {
        y: 100,
        opacity: 0,
        scale: 0.9,
        scrollTrigger: {
          trigger: sectionRef.current,
          start: 'top 80%',
          end: 'top 20%',
          scrub: 1,            // animação proporcional ao scroll
        },
      });
    }, sectionRef);

    return () => ctx.revert();
  }, []);

  return (
    <section ref={sectionRef}>
      <h2 ref={titleRef}>Título que emerge</h2>
    </section>
  );
}
```

### Framer Motion pattern

```tsx
import { motion, useScroll, useTransform } from 'framer-motion';

function ImmersiveSection() {
  const ref = useRef(null);
  const { scrollYProgress } = useScroll({
    target: ref,
    offset: ['start end', 'end start'],
  });

  const y = useTransform(scrollYProgress, [0, 1], [100, -100]);
  const opacity = useTransform(scrollYProgress, [0, 0.3, 0.7, 1], [0, 1, 1, 0]);
  const scale = useTransform(scrollYProgress, [0, 0.5], [0.9, 1]);

  return (
    <section ref={ref}>
      <motion.h2 style={{ y, opacity, scale }}>
        Título que emerge
      </motion.h2>
    </section>
  );
}
```

---

<a id="performance"></a>
## 12. Performance — O Limite da Imersão

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
## 13. Degradação Elegante (Mobile e Low-End)

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
## 14. Anti-Padrões Imersivos

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
