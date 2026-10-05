# Maleta del cambio

Página estática en un solo `index.html` (sin paso de compilación).

## Librerías que se usan siempre
- Lenis: scroll suave
- GSAP: animaciones de entrada e interacciones
- Vanta (con three.js): fondo animado
- React Bits (https://github.com/DavidHDev/react-bits): se usa sin compilar, vía importmap + esm.sh, portando el componente a `React.createElement`. Ya está GradientText en el título. Licencia MIT + Commons Clause: usar en el sitio, no redistribuir los componentes.
- Cargar todas por CDN. Proteger el código para que la página funcione si alguna no carga.
- Respetar `prefers-reduced-motion`.

## Notas
- Mantener la paleta actual (azules, naranja y café de la maleta) y los textos en español.
