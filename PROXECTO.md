# Xadrez de Coalicións — documento de traspaso

Se tes que continuar este proxecto noutra conversa, pega este ficheiro enteiro e
adxunta o zip `xadrez-coalicions-app.zip`. Con iso hai contexto de sobra.

---

## 1. Que é

App web do **Xadrez de Coalicións**, o xogo de catro potencias que ideou Arnold
Schönberg arredor de 1920. Xógase nun taboleiro de 10×10, con catro bandos, dos
cales dous teñen Rei (Amarelo e Negro, "potencias maiores") e dous non (Verde e
Vermello, "potencias menores"). A graza do xogo é que as menores deciden con
quen se alían durante as tres primeiras roldas.

Idioma principal **galego**, con castelán, inglés e alemán.

Proxecto persoal, sen ánimo de lucro. Ten que ser **gratis** de manter.

---

## 2. Estado actual: rematado e funcionando

- Motor de xogo completo e fiel ás regras orixinais (probado con partidas aleatorias).
- Menú inicial ilustrado, con animación de entrada.
- 8 estilos de pezas e 5 taboleiros.
- Son: efectos e música dodecafónica xerada en directo.
- Modo en liña con salas, probado entre catro pestanas. **Falta probalo contra
  Firebase real.**
- Instalable como app (PWA), funciona sen conexión.
- Manual de regras con diagramas e explicación de cada movemento.

### O que queda por facer
1. Probar o modo en liña contra Firebase de verdade.
2. O título alemán do menú ten un "S" de máis: pon KOALITIONSSSCHACH e debería
   ser KOALITIONSSCHACH.
3. Opcional: bots para xogar sendo menos de catro. A arquitectura xa está
   preparada (cada posto é independente), pero non hai IA.
4. Opcional: mellorar a música cargando material dun MIDI real.

---

## 3. Como está feito

**Un só ficheiro `index.html`** con todo dentro: HTML, CSS e JavaScript. Sen
framework, sen compilar, sen dependencias. Ábrese e funciona.

```
app/
├── index.html              ← TODO o código (motor, menú, son, rede)
├── manifest.webmanifest    ← para instalar como app
├── sw.js                   ← caché sen conexión. OLLO: ver punto 6
├── icona-*.png             ← iconas da app
├── fontes/                 ← 4 .woff incrustadas (Adventor Bold + Poppins)
├── menu/                   ← figuras do menú, títulos e palabras dos botóns
│   └── txt/                ← 16 palabras: 4 botóns × 4 idiomas
├── taboleiros/             ← boliazul.webp (o taboleiro debuxado a man)
└── pezas/                  ← 288 imaxes: 8 skins × 4 cores × 9 pezas
```

### Decisións técnicas que convén non desfacer

- **O tamaño do taboleiro calcúlase en JavaScript** (`axustarTaboleiro()`), non
  en CSS. Probouse con CSS e deformábase en certas proporcións de pantalla.
- **As pezas cenitais rotan por facción** (Amarelo 0°, Negro 180°, Vermello 90°,
  Verde −90°) e as de perspectiva non. En modo "primeira persoa" o taboleiro
  rota e as pezas compensan.
- **O chan do menú é SVG calculado**, non un degradado cónico con 3D: probouse e
  fallaba en Safari.
- **As imaxes das pezas gárdanse unha vez por cor**, non recoloréanse por CSS,
  porque as skins xeradas por IA non tiñan o recheo plano necesario.
- **As palabras dos botóns son imaxes**, normalizadas a unha altura de maiúscula
  común, porque cada unha viña ocupando o mesmo ancho e "PLAY" tiña letras o
  dobre de grandes que "SETTINGS".

---

## 4. As regras implementadas

Fontes: o **Arnold Schönberg Center** (fonte principal) e a implantación de
referencia de M. Paquette (detalles adicionais).

- Taboleiro 10×10, columnas A–K (sen a I), filas 1–10.
- Orde de quenda fixa: Amarelo → Negro → Verde → Vermello.
- Nove pezas: Rei, Avión (dous saltos de cabalo seguidos, sen volver á orixe),
  Submarino (Dama + Cabalo), Tanque (Dama), Artillería (Torre), Enxeñeiro
  (Cabalo), Motorista (Bispo), Gardián (peón), Metralladora (Rei + dobre paso
  inicial, capturable sen perder).
- **Coalicións**: nas roldas 1–3 cada potencia menor declara aliada, ou queda
  aliada automaticamente se captura ou dá xaque a unha maior (queda aliada da
  *outra*). Cada potencia maior só pode ter unha aliada: manda quen declare
  antes, e a orde de quenda dá vantaxe a Verde.
- **Regra especial de Vermello**: por mover o último, non pode aliarse con
  Amarelo na rolda 1 a menos que alguén xa atacase a Amarelo. Se se alía con
  Amarelo na rolda 3, non recibe puntos aínda que gañe.
- Se ao acabar a rolda 3 unha menor segue neutral, a partida detense: 1 punto
  aos aliados, 0 a quen non se comprometeu.
- As catro esquinas son neutrais ata que todas as alianzas estean decididas.
- Xaque respóndese de inmediato, fóra de orde, e pérdese a quenda seguinte.
- Puntuación: 6 por gañar, 2 por táboas.
- Variante opcional: menores (Verde+Vermello) contra maiores.

---

## 5. O modo en liña

Adaptador con **dúas implantacións** intercambiables:

- **LocalDB**: entre pestanas do mesmo navegador, vía localStorage. Actívase soa
  cando non hai configuración de Firebase. Serve para probar sen Internet.
- **Firebase Realtime Database**: para xogar de verdade. Só hai que pegar a
  configuración.

Funcionamento: unha sala é un nodo `rooms/CODIGO` con quen a creou, os catro
postos, os nomes, o estado completo da partida serializado e o chat. Tras cada
acción quen a fixo publica o estado enteiro e os demais redebuxan.

Límites: 20 salas activas, e as salas péchanse aos 30 minutos sen actividade.
Sen contas nin contrasinais: só un alcume e un identificador anónimo.

**Para probar catro xogadores nunha soa máquina**, engade `?uid=1`, `?uid=2`…
ao enderezo de cada pestana. Sen iso comparten identificador.

---

## 6. Aviso importante para quen toque isto

Cada vez que se publique unha versión nova hai que cambiar a liña

```js
const VERSION = "xc-v2";
```

do ficheiro `sw.js` (a `xc-v3`, `xc-v4`…). Se non, os dispositivos que xa teñan
a app seguirán a amosar a versión vella gardada na caché e parecerá que os
cambios non se aplicaron. É o erro máis fácil de cometer.

Se se engade algún ficheiro novo (unha skin, un taboleiro), hai que engadilo
tamén á lista `ARQUIVOS` do `sw.js`.

---

## 7. Valores por defecto actuais

| Axuste | Por defecto |
|---|---|
| Idioma | Galego |
| Estilo das pezas | Minimal |
| Taboleiro | Madeira |
| Orientación | Fixa |
| Música | Apagada |
| Efectos de son | Acesos |
| Coordenadas | Visibles |

---

## 8. Estética

Inspirada no cartel construtivista dos anos 20, contemporáneo de Schönberg.
Cores das catro potencias: `#F0B800` amarelo, `#1F2228` negro, `#2C8A55` verde,
`#D8382A` vermello. Papel `#EDEAE2`, tinta `#12141A`.

Tipografía: TeX Gyre Adventor Bold para títulos e botóns (condensada grosa),
Poppins para o texto corrido. Ambas libres e incrustadas na app.
