# Modo en liña — como poñelo en marcha (gratis)

A app xa trae todo o código do modo en liña. Só hai que darlle unha base de datos
en tempo real. Usamos **Firebase Realtime Database** na súa capa gratuíta (Spark),
que chega de sobra para 20 salas activas.

## 1. Crear o proxecto (10 minutos, sen tarxeta)

1. Entra en https://console.firebase.google.com e crea un proxecto (calquera nome).
2. No menú lateral: **Build → Realtime Database → Create database**.
   Escolle a rexión europea e comeza en **modo de proba** (logo poñemos regras).
3. Vai a **Project settings (roda dentada) → General → Your apps → Web (</>)**.
   Rexistra unha app web e copia o obxecto `firebaseConfig` que che dá.

## 2. Pegar a configuración na app

Abre `index.html`, busca esta liña (está preto do final):

    const FIREBASE_CONFIG = null;

e substitúe `null` polo obxecto copiado, por exemplo:

    const FIREBASE_CONFIG = {
      apiKey: "AIza...",
      databaseURL: "https://o-teu-proxecto-default-rtdb.europe-west1.firebasedatabase.app",
      projectId: "o-teu-proxecto",
    };

Con `databaseURL` e `projectId` abonda; o resto de campos poden quedar.

## 3. Regras da base de datos

En **Realtime Database → Rules**, pega isto e publica:

    {
      "rules": {
        "rooms": {
          "$sala": {
            ".read": true,
            ".write": true,
            ".validate": "newData.hasChildren(['created','host']) || !newData.exists()"
          }
        }
      }
    }

É un xogo sen contas, así que as regras son abertas: calquera que teña o código
da sala pode ler e escribir nela. É o mesmo modelo que un Jackbox ou un Kahoot.

## 4. Publicar a app

A app é estática: cópiaa enteira (index.html + pezas/ + menu/) a calquera
aloxamento gratuíto:

- **GitHub Pages** (recomendado): sobe a carpeta a un repositorio e activa Pages.
- Netlify ou Vercel: arrastra a carpeta e listo.

Non hai servidor propio nin nada que manter.

## Como funciona por dentro

- Cada dispositivo garda un identificador anónimo. Non hai contas.
- Unha sala é un nodo `rooms/CODIGO` con: quen a creou, os catro postos, os
  nomes, o estado completo da partida (serializado) e o chat.
- Tras cada acción, quen a fixo escribe o estado enteiro; os demais reciben o
  cambio e redebuxan. Son uns poucos KB por xogada: irrelevante para a capa gratuíta.
- Só pode mover quen ocupa o posto ao que lle toca. "Desfacer" non existe en liña.
- Límite: 20 salas activas (as que tiveron actividade nos últimos 30 minutos).
  As salas mortas límpanse soas cando alguén crea unha nova.
- Se pechas o navegador, ao volver a app lembra a última sala e o teu posto.

## Probar sen Firebase

Mentres `FIREBASE_CONFIG` sexa `null`, o modo en liña funciona **entre pestanas
do mesmo navegador** (usa localStorage). Abre catro pestanas, crea unha sala nunha
e únete nas outras co código. Serve para probar todo o fluxo sen Internet.
