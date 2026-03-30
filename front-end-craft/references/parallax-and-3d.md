# Parallax, 3D e Cursor — Profundidade e Interatividade

Parallax com mouse e scroll, CSS 3D transforms, Three.js/WebGL, cursor customizado, magnetic buttons, e cards 3D com tilt.

## Table of Contents
1. [Profundidade e Parallax com Propósito](#parallax)
2. [3D com CSS e WebGL](#3d)
3. [Cursor como Presença](#cursor)
4. [Cards e Elementos com Profundidade](#cards-3d)

---

<a id="parallax"></a>
## 1. Profundidade e Parallax com Propósito

### O parallax certo vs. o parallax errado

**Errado**: Imagem de fundo se move em velocidade diferente do conteúdo — clichê dos anos 2010, causa motion sickness.

**Certo**: Elementos DENTRO da composição existem em camadas de profundidade. Quando o visitante scrolla, cada camada se move em velocidade diferente — criando profundidade real, como olhar através de uma janela.

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
## 2. 3D com CSS e WebGL

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
function HeroBackground() {
  const containerRef = useRef<HTMLDivElement>(null);
  useEffect(() => {
    const container = containerRef.current;
    if (!container) return;
    const scene = new THREE.Scene();
    const camera = new THREE.PerspectiveCamera(75, innerWidth / innerHeight, 0.1, 1000);
    const renderer = new THREE.WebGLRenderer({ alpha: true, antialias: true });
    renderer.setSize(innerWidth, innerHeight);
    renderer.setPixelRatio(Math.min(devicePixelRatio, 2));
    container.appendChild(renderer.domElement);
    camera.position.z = 5;
    // Mouse interaction
    const mouse = new THREE.Vector2();
    const onMouseMove = (e: MouseEvent) => {
      mouse.x = (e.clientX / innerWidth) * 2 - 1;
      mouse.y = -(e.clientY / innerHeight) * 2 + 1;
    };
    addEventListener('mousemove', onMouseMove);
    // Render loop
    const animate = () => { requestAnimationFrame(animate); renderer.render(scene, camera); };
    animate();
    // Resize
    const onResize = () => { camera.aspect = innerWidth / innerHeight; camera.updateProjectionMatrix(); renderer.setSize(innerWidth, innerHeight); };
    addEventListener('resize', onResize);
    return () => { removeEventListener('mousemove', onMouseMove); removeEventListener('resize', onResize); renderer.dispose(); container.removeChild(renderer.domElement); };
  }, []);
  return <div ref={containerRef} style={{ position: 'absolute', inset: 0, zIndex: 0 }} />;
}
```

### Cenas 3D que funcionam para LPs

- **Part��culas flutuantes** — reagem ao cursor, formam shapes abstratas. Para: tech, cripto, SaaS, agências.
- **Gradiente mesh 3D** — superfície ondulada com gradiente. Para: qualquer brand premium.
- **Objeto product showcase** — produto 3D flutuando, reagindo ao mouse. Para: e-commerce premium, joalheria, tech hardware.
- **Terreno abstrato** — mesh wireframe que ondula. Para: fintech, imobiliária.

### Regras de uso do Three.js

1. **Apenas UMA cena 3D por página** — geralmente no hero. Múltiplas cenas WebGL matam a performance.
2. **Canvas com `alpha: true`** — fundo transparente, o 3D se integra ao layout.
3. **`setPixelRatio(Math.min(devicePixelRatio, 2))`** — cap em 2x, telas retina em 3x destroem performance.
4. **Dispose on unmount** — limpar geometrias, materiais, texturas. Memory leak é real.
5. **Fallback para mobile** — detectar device com `navigator.maxTouchPoints > 0` ou matchMedia. Em mobile, simplificar drasticamente ou substituir por CSS.

---

<a id="cursor"></a>
## 3. Cursor como Presença

### Custom cursor

Um cursor customizado faz o visitante sentir que está "dentro" do site:

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
## 4. Cards e Elementos com Profundidade

### O 3D tilt card

O efeito mais impactante com o melhor custo-benefício — cards que inclinam em 3D conforme o cursor:

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
