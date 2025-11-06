# AutoQuote 3D — Fast Deploy Repository Structure (Ready for GitHub)

Este repositorio contiene todo lo necesario para desplegar el MVP de **AutoQuote 3D** en **Render** con mínima configuración. Incluye frontend (Next.js), backend API, worker de slicing con CuraEngine y configuración Docker completa.

---

## 🧩 Estructura del Repositorio

```
autoquote-3d/
├── frontend/                 # Frontend + API (Next.js)
│   ├── pages/
│   │   ├── index.tsx
│   │   ├── api/
│   │   │   ├── upload.ts
│   │   │   ├── checkout.ts
│   │   │   └── webhook.ts
│   ├── components/
│   │   ├── Viewer3D.tsx
│   │   └── Configurator.tsx
│   ├── lib/
│   │   ├── db.ts
│   │   └── s3.ts
│   ├── package.json
│   ├── next.config.js
│   ├── Dockerfile
│   └── tsconfig.json
│
├── worker/                   # Worker background job (CuraEngine)
│   ├── worker.js
│   ├── Dockerfile
│   └── package.json
│
├── render.yaml                # Render deployment descriptor
├── docker-compose.yml         # Local testing
├── .env.example               # Variables de entorno
└── README.md
```

---

## ⚙️ Frontend Dockerfile
```Dockerfile
# frontend/Dockerfile
FROM node:18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build
EXPOSE 3000
CMD ["npm", "start"]
```

---

## ⚙️ Worker Dockerfile
```Dockerfile
# worker/Dockerfile
FROM ubuntu:22.04
RUN apt-get update && apt-get install -y nodejs npm cura-engine && rm -rf /var/lib/apt/lists/*
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
CMD ["node", "worker.js"]
```

---

## 🧾 render.yaml
Archivo de configuración para despliegue rápido en Render.

```yaml
services:
  - type: web
    name: autoquote3d-frontend
    env: node
    plan: free
    buildCommand: "npm install && npm run build"
    startCommand: "npm start"
    rootDir: frontend
    envVars:
      - key: MONGO_URI
        sync: false
      - key: STRIPE_SECRET
        sync: false
      - key: S3_KEY
        sync: false
      - key: S3_SECRET
        sync: false
      - key: S3_BUCKET
        sync: false
      - key: APP_URL
        value: https://autoquote3d.onrender.com

  - type: worker
    name: autoquote3d-worker
    env: docker
    plan: free
    rootDir: worker
    envVars:
      - key: MONGO_URI
        sync: false
      - key: S3_KEY
        sync: false
      - key: S3_SECRET
        sync: false
      - key: S3_BUCKET
        sync: false
```

---

## 🧱 Ejemplo .env.example
```
MONGO_URI=mongodb+srv://<user>:<pass>@cluster.mongodb.net/autoquote3d
STRIPE_SECRET=sk_live_xxx
S3_KEY=AKIAxxxx
S3_SECRET=xxxx
S3_BUCKET=autoquote3d-files
APP_URL=https://autoquote3d.onrender.com
```

---

## 📦 README.md (extracto)
```markdown
# AutoQuote 3D — Prototype for Fast Deployment

Un sistema simple y efectivo de cotización automática para impresión 3D.

## Instalación local
```bash
git clone https://github.com/<tu_usuario>/autoquote-3d.git
cd autoquote-3d/frontend
npm install
npm run dev
```

## Despliegue en Render
1. Subir este repo a GitHub.
2. Crear servicio Web en Render apuntando a `/frontend`.
3. Crear Worker en Render apuntando a `/worker`.
4. Añadir variables de entorno del archivo `.env.example`.
5. Activar webhook de Stripe.

## Flujo básico
1. Usuario sube STL/OBJ.
2. Sistema estima volumen y precio.
3. Usuario paga con Stripe.
4. Worker procesa modelo con CuraEngine y actualiza DB.
```

---

## 🚀 Siguientes pasos automáticos
- Conectar GitHub → Render (deploy automático).
- Validar `.env` con claves Stripe y Mongo.
- Subir primeros archivos STL de prueba.
- Verificar flujo: Upload → Price → Pay → Slicing.

---

¿Quieres que genere el **contenido de cada archivo (código completo)** para subirlo directamente al repo GitHub y probar en Render? (frontend, api, worker y Dockerfiles con ejemplos reales).

