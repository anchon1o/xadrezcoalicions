# Poñer a app en marcha — orde de pasos

Guía práctica, en orde. Tempo total: **20-30 minutos**, ou 5 se te quedas coa
versión sen xogo en liña.

---

## Antes de nada: cantas contas necesito?

Depende de ata onde queiras chegar. Podes parar en calquera nivel.

| Nivel | Que consegues | Contas |
|---|---|---|
| **1** | Xogar ti no ordenador | ningunha |
| **2** | Que calquera entre dende o móbil, e instalala | 1 (Netlify **ou** GitHub) |
| **3** | Xogar catro persoas a distancia | 2 (a anterior + Google, que xa terás) |

Todas son **gratuítas** e ningunha pide tarxeta.

---

# NIVEL 1 · Probala na túa máquina (5 minutos, sen contas)

1. Descomprime `xadrez-coalicions-app.zip`. Queda unha carpeta `app/`.
2. Entra nela e fai dobre clic en **`index.html`**.
3. Ábrese no navegador. Xa podes xogar os catro no mesmo dispositivo.

Neste nivel funciona todo menos dúas cousas: instalala como app e o modo sen
conexión. Para iso fai falta publicala (nivel 2).

**Para probar o xogo en liña sen publicar nada**: abre `index.html` catro veces
en catro pestanas, e en cada unha engade `?uid=1`, `?uid=2`, `?uid=3`, `?uid=4`
ao final do enderezo. Crea sala nunha e únete nas outras co código.

---

# NIVEL 2 · Publicala en Internet

Escolle **unha** das dúas. A primeira é máis rápida, a segunda dura máis.

## Opción A · Netlify (a máis fácil, 5 minutos)

**Crear a conta**
1. Entra en https://app.netlify.com/signup
2. Preme **Sign up with email** (ou con GitHub, se prefires).
3. Pon correo e contrasinal. Confirma o correo que che chega.

**Subir a app**
4. Entra en https://app.netlify.com/drop
5. **Arrastra a carpeta `app/` enteira** á zona grande do medio.
6. En 10-20 segundos dáche un enderezo tipo
   `https://palabra-aleatoria-123456.netlify.app`. Xa está publicada.

**Poñerlle un nome decente**
7. No panel: **Site configuration → Change site name**.
8. Escribe por exemplo `xadrez-coalicions`. O enderezo pasa a ser
   `https://xadrez-coalicions.netlify.app`.

**Para actualizar despois**: no panel do sitio, pestana **Deploys**, arrastra
outra vez a carpeta `app/`.

## Opción B · GitHub Pages (10 minutos, máis duradeiro)

**Crear a conta**
1. Entra en https://github.com/signup
2. Correo, contrasinal e nome de usuario. Confirma o correo.

**Crear o repositorio**
3. Arriba á dereita, **+ → New repository**.
4. *Repository name*: `xadrez-coalicions`
5. Marca **Public**. Non marques nada máis. **Create repository**.

**Subir os ficheiros**
6. Na páxina que aparece, preme **uploading an existing file**.
7. Abre a carpeta `app/` e arrastra **todo o que hai dentro** (o `index.html`, o
   `sw.js`, e as carpetas `pezas`, `menu`, `fontes`, `taboleiros`).
   > Importante: o `index.html` ten que quedar na **raíz** do repositorio, non
   > dentro dunha carpeta `app`.
   > Se o navegador non che deixa arrastrar carpetas, instala
   > **GitHub Desktop** (https://desktop.github.com), que si o fai ben.
8. Abaixo do todo, botón verde **Commit changes**.

**Activar a publicación**
9. Pestana **Settings** (arriba) → **Pages** (menú da esquerda).
10. *Source*: **Deploy from a branch**.
11. *Branch*: **main**, carpeta **/ (root)**. Preme **Save**.
12. Agarda 1-3 minutos e recarga a páxina. Amosarache o enderezo:
    `https://ousuario.github.io/xadrez-coalicions/`

**Para actualizar despois**: no repositorio, **Add file → Upload files**, sobe
os ficheiros cambiados e **Commit changes**.

---

# NIVEL 3 · Activar o xogo a distancia (15 minutos)

Só se queres que catro persoas xoguen dende sitios distintos. Precisa unha conta
de Google, que probablemente xa teñas.

## 3.1 Crear a base de datos

1. Entra en https://console.firebase.google.com e inicia sesión coa túa conta de
   Google.
2. **Create a project** (ou *Add project*).
   - Nome: `xadrez-coalicions`
   - Desmarca Google Analytics, non fai falta.
   - **Create project**. Tarda un minuto.
3. No menú da esquerda: **Build → Realtime Database**.
   - **Create database**
   - *Location*: escolle **europe-west1** (Bélxica).
   - *Security rules*: **Start in test mode** → **Enable**.
4. Copia o enderezo que aparece arriba da base de datos. É algo así:
   `https://xadrez-coalicions-default-rtdb.europe-west1.firebasedatabase.app`

## 3.2 Rexistrar a app web

5. Roda dentada (arriba á esquerda) → **Project settings**.
6. Baixa ata **Your apps** e preme a icona **`</>`** (web).
7. *App nickname*: `web`. **Non** marques Firebase Hosting. **Register app**.
8. Amósache un bloque de código. Fíxate nestes dous valores:
   - `apiKey: "AIza..."`
   - `projectId: "xadrez-coalicions"`

## 3.3 Pegalo na app

9. Abre `index.html` cun editor de texto.
   - Windows: Notepad, ou mellor **Notepad++**.
   - Mac: TextEdit en *Formato → Convertir en texto normal*, ou **VS Code**.
10. Busca (Ctrl+F / Cmd+F) esta liña:
    ```js
    const FIREBASE_CONFIG = null;
    ```
11. Substitúea por esta, cos teus datos:
    ```js
    const FIREBASE_CONFIG = {
      apiKey: "AIza...",
      databaseURL: "https://xadrez-coalicions-default-rtdb.europe-west1.firebasedatabase.app",
      projectId: "xadrez-coalicions"
    };
    ```
12. Garda o ficheiro.

## 3.4 Publicar as regras

13. Volve a Firebase: **Realtime Database → Rules**.
14. Borra o que haxa e pega isto:
    ```json
    {
      "rules": {
        "rooms": {
          "$sala": { ".read": true, ".write": true }
        }
      }
    }
    ```
15. **Publish**. Firebase avisarate de que son regras abertas: é o esperado, o
    xogo non ten contas e quen teña o código da sala pode xogar nela.

## 3.5 Subir a versión nova

16. Abre `sw.js` e cambia `const VERSION = "xc-v3";` por `"xc-v3"`.
17. Volve subir a carpeta ao sitio de publicación (Netlify ou GitHub).

## 3.6 Probalo

18. Abre o enderezo en dous dispositivos distintos (o teu móbil e o ordenador).
19. Nun: **En liña → nome → Crear**. Aparece un código de 5 letras.
20. No outro: **En liña → nome → escribe o código → Entrar**.
21. Cada un escolle a súa cor e, quen creou a sala, preme **Comezar**.

---

# Instalar a app no móbil

Cando xa estea publicada (nivel 2 ou 3):

**iPhone / iPad**
1. Abre o enderezo **en Safari**. Non vale dende Instagram, Telegram nin outra
   app: ten que ser Safari.
2. Botón **compartir** (cadrado cunha frecha cara arriba).
3. Baixa na lista e escolle **Engadir á pantalla de inicio**.

**Android**
1. Abre o enderezo en **Chrome**.
2. Menú **⋮** → **Instalar aplicación** (ou *Engadir á pantalla de inicio*).

**Ordenador (Chrome ou Edge)**
Na barra de enderezos aparece unha icona de instalar. Ou menú **⋮ → Instalar**.

---

# Problemas típicos

**Vexo o menú sen debuxos.**
Subiches só o `index.html`. Fan falta tamén as carpetas `pezas`, `menu`,
`fontes` e `taboleiros`, ao seu lado.

**Subín cambios e non se ven.**
Non cambiaches a `VERSION` do `sw.js`. Cámbiaa, sobe outra vez, e recarga
forzando: Ctrl+Shift+R (Cmd+Shift+R no Mac).

**En GitHub Pages dá erro 404.**
O `index.html` quedou dentro dunha carpeta `app`. Ten que estar na raíz.

**"Non se puido conectar" no modo en liña.**
Revisa que copiaches ben o `databaseURL` (que remate en
`.firebasedatabase.app`, sen barra final) e que publicaches as regras.

**No iPhone non me sae "Engadir á pantalla de inicio".**
Non estás en Safari, ou estás abrindo o ficheiro local en vez do enderezo
publicado.

**Non soa nada.**
Os navegadores esixen que toques a pantalla unha vez antes de permitir audio.
Toca no taboleiro. E comproba en Axustes que a música está acesa: vén apagada.
