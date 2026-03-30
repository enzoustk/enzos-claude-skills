# Scroll, Animação e Stack Técnico — De Flat para Dimensional

Este arquivo cobre a filosofia de imersão, scroll-driven animations, text reveals, smooth scroll (Lenis), stack técnico, e patterns GSAP/Framer Motion.

## Table of Contents
1. [Filosofia: De Flat para Dimensional](#filosofia)
2. [Scroll como Narrativa](#scroll-narrativa)
3. [Texto que Vive](#texto-vivo)
4. [Smooth Scroll (Lenis)](#smooth-scroll)
5. [Stack Técnico Imersivo](#stack)

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

<a id="texto-vivo"></a>
## 3. Texto que Vive

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

<a id="smooth-scroll"></a>
## 4. Smooth Scroll (Lenis)

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
## 5. Stack Técnico Imersivo

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
