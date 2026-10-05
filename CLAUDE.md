# Maleta del cambio

Página estática en un solo `index.html` (sin React ni paso de compilación).

## Librerías que se usan siempre
- Lenis: scroll suave
- GSAP: animaciones de entrada e interacciones
- Vanta (con three.js): fondo animado
- Cargar todas por CDN. Proteger el código para que la página funcione si alguna no carga.
- Respetar `prefers-reduced-motion`.

## Notas
- react-bits son componentes de React: no se pueden usar aquí sin migrar a Vite + React. Si hace falta un efecto parecido, recrearlo con GSAP/CSS.
- Mantener la paleta actual (azules, naranja y café de la maleta) y los textos en español.
