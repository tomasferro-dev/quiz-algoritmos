# Configurar el ranking con Firebase

El juego ya tiene todo el código del ranking listo. Solo falta **pegar tus
credenciales de Firebase** y poner las reglas de seguridad. Son ~5 minutos.

## 1. Crear el proyecto

1. Entrá a https://console.firebase.google.com y logueate con tu cuenta de Google.
2. Tocá **"Agregar proyecto"** → ponele un nombre (ej. `algo-trivia`) → seguí
   los pasos (podés desactivar Google Analytics, no hace falta).

## 2. Crear la base de datos (Firestore)

1. En el menú lateral: **Compilación → Firestore Database**.
2. Tocá **"Crear base de datos"**.
3. Elegí **modo de producción** (luego pegamos las reglas del paso 4) y la
   ubicación más cercana (ej. `southamerica-east1`). Crear.

## 3. Obtener las credenciales (firebaseConfig)

1. Tocá el engranaje ⚙️ (arriba a la izquierda) → **Configuración del proyecto**.
2. Bajá hasta **"Tus apps"** → tocá el icono **`</>`** (Web).
3. Registrá la app (un apodo cualquiera, NO marques Hosting) → **Registrar app**.
4. Firebase te muestra un objeto `firebaseConfig = { ... }`. Copialo.
5. Abrí `index.html` y reemplazá el bloque marcado con
   `🔻 REEMPLAZA ESTE OBJETO 🔻` por el tuyo. Debe quedar así:

   ```js
   const firebaseConfig = {
     apiKey: "AIza....",
     authDomain: "algo-trivia.firebaseapp.com",
     projectId: "algo-trivia",
     storageBucket: "algo-trivia.appspot.com",
     messagingSenderId: "1234567890",
     appId: "1:1234:web:abcd...."
   };
   ```

## 4. Reglas de seguridad (importante)

En **Firestore Database → pestaña "Reglas"**, pegá esto y publicá:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /rankings/{quiz}/scores/{doc} {
      allow read: if true;
      allow create: if request.resource.data.score is number
                    && request.resource.data.name is string
                    && request.resource.data.name.size() <= 30;
      allow update, delete: if false;
    }
  }
}
```

Esto permite: leer el ranking (todos) y crear un puntaje nuevo, pero NO editar
ni borrar puntajes ajenos.

## 5. Subir el cambio

Como ya tenés GitHub → Vercel:

```
git add index.html
git commit -m "Agregar ranking con Firebase"
git push
```

Vercel redeploya solo. Listo: al terminar un juego el puntaje se guarda y
todos (incluido vos) ven el ranking en vivo desde "🏆 Ver ranking".

---

## Notas
- Es gratis para un curso (el plan Spark de Firebase sobra).
- El ranking es por juego (Algo-Trivia y Detective de Código tienen su propia tabla).
- No es a prueba de trampas: alguien con conocimientos podría enviar un puntaje
  falso. Para una nota formal, usá la captura como respaldo.
- Para "limpiar" el ranking entre cursos, borrá la colección `rankings` desde
  la consola de Firestore.
