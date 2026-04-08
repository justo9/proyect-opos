# 🎷 auladesaxofon.es — Guía de configuración completa

## Archivos
- `index.html` — la app completa
- `manifest.json` — PWA (instalable en móvil)
- `sw.js` — funciona sin conexión
- `icon-192.png` / `icon-512.png` — pon aquí iconos de 192×192 y 512×512 px

---

## PASO 1 — Firebase (15 min)

### 1.1 Crear proyecto
1. Ve a https://console.firebase.google.com
2. **Añadir proyecto** → nombre: `aula-saxofon` → sin Analytics → **Crear**

### 1.2 Authentication
3. Menú izquierdo → **Authentication** → **Comenzar**
4. Pestaña **Sign-in method** → habilita **Correo/contraseña**
5. Pestaña **Users** → **Añadir usuario** → tu email de profesor + contraseña

### 1.3 Firestore
6. Menú izquierdo → **Firestore Database** → **Crear base de datos**
7. Modo producción → región `eur3 (europe-west)` → **Listo**

### 1.4 Reglas de Firestore
8. Firestore → pestaña **Reglas** → sustituye todo por esto y publica:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /alumnos/{pin} {
      allow read, write: if true;
    }
    match /eventos/{id} {
      allow read: if true;
      allow write: if request.auth != null;
    }
  }
}
```

### 1.5 Obtener configuración
9. ⚙️ → **Configuración del proyecto** → sección **Tus apps** → icono `</>`
10. Registra app web → copia el objeto `firebaseConfig`
11. Abre `index.html`, busca la sección **CONFIGURA AQUÍ TU FIREBASE** y pega tus valores reales

---

## PASO 2 — Crear alumnos en Firestore

Para cada alumno añade un documento en la colección `alumnos`:

**ID del documento** = PIN del alumno (ej: `1234`)

**Campos del documento:**
```json
{
  "nombre": "Ana García López",
  "pin": "1234",
  "trimestre": 1,
  "tecnica": [],
  "estudios": [],
  "obras": [],
  "transcripciones": [],
  "tecnicas": {},
  "progreso": { "t1": 0, "t2": 0, "t3": 0 }
}
```

Repite con un PIN distinto para cada alumno (ej: `5678` para el segundo).

---

## PASO 3 — Subir a GitHub Pages

```bash
# Crea un nuevo repositorio en github.com
# Nombre sugerido: auladesaxofon

git init
git add .
git commit -m "Aula de Saxofón - versión inicial"
git remote add origin https://github.com/TU_USUARIO/auladesaxofon.git
git push -u origin main
```

En GitHub → Settings → Pages → Branch: **main** → carpeta **/ (root)** → **Save**

La app queda en: `https://TU_USUARIO.github.io/auladesaxofon`

---

## PASO 4 — Apuntar auladesaxofon.es a GitHub Pages

En Hostinger (igual que hiciste con Manteconni):

**DNS → Añadir registros:**
```
Tipo A  →  185.199.108.153
Tipo A  →  185.199.109.153
Tipo A  →  185.199.110.153
Tipo A  →  185.199.111.153
CNAME   →  www  →  TU_USUARIO.github.io
```

En GitHub → Settings → Pages → Custom domain → escribe `auladesaxofon.es` → **Save**

Activa **Enforce HTTPS** (puede tardar unos minutos en aparecer).

---

## PASO 5 — Instalar como app en el móvil

**Android (Chrome):** menú ⋮ → Añadir a pantalla de inicio

**iPhone (Safari):** botón compartir → Añadir a pantalla de inicio

---

## Flujo de uso

| Quién | Cómo entra | Qué puede hacer |
|-------|-----------|----------------|
| Alumno | PIN de 4 dígitos | Ver contenido asignado, marcar estado, añadir notas propias, ver su progreso y calendario |
| Profesor | Email + contraseña | Añadir/eliminar ítems en todas las secciones, poner comentarios, ajustar barras de progreso, gestionar calendario |

---

## Añadir más alumnos en el futuro

Solo añade un documento nuevo en Firestore → colección `alumnos` → con un PIN distinto. Sin tocar el código.
