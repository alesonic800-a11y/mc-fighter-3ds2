# Moncloa's Fighter - port nativo para Nintendo 3DS (5 luchadores)

Luchadores: Perro Sanxe, Alberto Freijoo, Isa Diaz Ayusa, Martinez-Almeja y M. Rajoy.
Modo arcade: eliges luchador y te enfrentas a los otros 4 (mejor de 3 rondas).

## Controles
PAD / STICK mover - ARRIBA saltar - ABAJO agacharse - A punyo - B patada - ABAJO+B patada baja
X especial 1 (proyectil) - Y especial 2 - R super (barra llena) - L (o atras) bloquear - START pausa
(L+R+START+SELECT a la vez: salir al menu HOME)

## Como conseguir el .cia (2 formas)
### A) Sin instalar nada: GitHub Actions
1. Crea un repositorio en GitHub y sube TODO el contenido de esta carpeta (incluida `.github/`).
2. Pestanya Actions > "Compilar 3DS" > Run workflow (o se lanza solo con cada push).
3. Al terminar, descarga el artefacto `moncloas-fighter-3ds`: contiene `moncloas_fighter.3dsx` y `moncloas_fighter.cia`.
   (Si solo sale el .3dsx es que fallo la descarga de makerom/bannertool: revisa las URL del workflow.)
### B) En tu PC
1. Instala devkitPro con el grupo `3ds-dev` (https://devkitpro.org/wiki/Getting_Started).
2. Instala `makerom` (Project_CTR) y `bannertool` y ponlos en el PATH.
3. `export DEVKITPRO=/opt/devkitpro` y ejecuta `make cia` (o `make 3dsx` para Homebrew Launcher).
Instala el .cia con FBI (o similar) desde la SD. Requiere CFW.

## Que incluye / que no
- Incluido: combate completo (golpes, patadas, bajas, aereos, bloqueo, combos con dano reducido), especial 1 y 2
  propios de cada luchador, super con barra, 3 rondas, temporizador, IA, 1 escenario (Cibeles), HUD y menus.
- Incluido en esta version modificada: musica original del menu y Cibeles, efectos originales de golpes/patadas/bloqueos/caidas, voces de los cinco luchadores y clips de KO/victoria.
- El audio se convierte a WAV PCM 16-bit estéreo a 22.05 kHz y se reproduce mediante NDSP/libctru. Los recursos se encuentran en `romfs/audio/`.
- No incluido: 2 jugadores, remates (fatalities), aliados/apoyos, objetos de escenario, modos extra.

## Rendimiento
Render por software al framebuffer: sprites ya escalados (1:1) y en el orden de memoria del framebuffer,
fondo pre-rotado (un solo memcpy por fotograma), alfa duro. Fotograma medio en PC: 0,11 ms. Pensado para 60 fps
en la 3DS antigua. El modo New 3DS activa el turbo de CPU.

## Estructura
source/main.c + source/audio.c (juego y audio NDSP) - romfs/ (assets ya convertidos) - tools/convert_assets.py (regenera romfs/ desde el
juego web) - tests/ (pruebas en PC con el mismo main.c) - Makefile, app.rsf, .github/workflows/build.yml

## Pruebas en PC (mismo main.c)
    mkdir -p shots && gcc -O2 -Wno-unused-function tests/host.c -lm -o t && ./t        # partida CPU vs CPU con capturas en shots/
    gcc -O1 -g -fsanitize=address,undefined tests/host_stress.c -lm -o s && ./s         # los 20 enfrentamientos bajo sanitizers
