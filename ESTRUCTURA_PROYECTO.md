# Estructura del proyecto

Este documento describe la estructura del repositorio `Taller-DevOps-Monorepo-CI` y explica brevemente el propósito de cada carpeta/archivo principal.

El repositorio es un **monorepo** con un backend en **Python (FastAPI)** y un frontend en **React + TypeScript**, además de configuraciones para **Docker** y **CI**.

---

## Árbol de archivos (resumido)

```text
Taller-DevOps-Monorepo-CI/
├── .github/
│   └── workflows/
│       └── ci.yml
├── .gitignore
├── README.md
├── docker-compose.yaml
│
├── backend/
│   ├── Dockerfile
│   ├── README.md
│   ├── requirements.txt
│   ├── main.py
│   │
│   ├── app/
│   │   ├── __init__.py
│   │   │
│   │   ├── core/
│   │   │   ├── __init__.py
│   │   │   ├── config.py
│   │   │   ├── logger.py
│   │   │   ├── middleware.py
│   │   │   └── security.py
│   │   │
│   │   ├── routers/
│   │   │   ├── __init__.py
│   │   │   ├── auth_router.py
│   │   │   ├── calculadora_router.py
│   │   │   └── health_router.py
│   │   │
│   │   ├── schemas/
│   │   │   ├── __init__.py
│   │   │   ├── auth.py
│   │   │   └── calculadora.py
│   │   │
│   │   └── services/
│   │       ├── __init__.py
│   │       └── calculadora_service.py
│   │
│   └── tests/
│       ├── __init__.py
│       ├── conftest.py
│       ├── test_auth.py
│       ├── test_calculadora.py
│       ├── test_cors.py
│       └── test_health.py
│
└── frontend/
    ├── Dockerfile
    ├── README.md
    ├── nginx.conf.template
    ├── package.json
    ├── tsconfig.json
    │
    ├── public/
    │   └── index.html
    │
    └── src/
        ├── App.tsx
        ├── App.css
        ├── Calculador.tsx
        ├── Calculadora.css
        ├── index.tsx
        ├── index.css
        ├── react-app-env.d.ts
        ├── reportWebVitals.ts
        ├── setupTests.ts
        ├── global.d.ts
        │
        ├── services/
        │   └── api.ts
        │
        ├── components/
        │   ├── Display.tsx
        │   ├── Historial.tsx
        │   ├── NumberPad.tsx
        │   ├── OperationPad.tsx
        │   └── StatusBar.tsx
        │
        ├── hooks/
        │   ├── index.ts
        │   ├── useAuth.ts
        │   └── useCalculadora.ts
        │
        ├── types/
        │   └── index.ts
        │
        └── assets/
            └── image/
                └── mate.png
```

---

## Explicación por carpetas y archivos principales

### Raíz

* `docker-compose.yaml` — Orquesta los servicios del **backend** y **frontend** para desarrollo y CI. El flujo de CI utiliza:

```bash
docker compose build
docker compose run --rm backend pytest
```

* `.github/workflows/ci.yml` — Configuración de **GitHub Actions**. Se ejecuta en los Pull Requests hacia la rama `main`. En este repositorio construye los contenedores y ejecuta los tests del backend.

* `README.md` — Documentación general del proyecto.

---

## Backend (`backend/`)

El backend consiste en una **API REST desarrollada con FastAPI**.

### `backend/main.py`

Es el punto de entrada de la aplicación FastAPI.

Configura:

* `root_path` en `/api`, por lo que las rutas y documentación se sirven bajo `/api`.
* `docs_url` y `redoc_url` para la documentación automática.
* `CORSMiddleware` para permitir peticiones desde el frontend.
* Los orígenes permitidos mediante la configuración definida en `app/core/config.py`.
* Un `LoggingMiddleware`.
* El ciclo de vida de la aplicación mediante `lifespan`.

Por ejemplo, la documentación de Swagger se encuentra en:

```text
http://localhost:8000/api/docs
```

### `backend/requirements.txt`

Contiene las dependencias utilizadas por el backend.

Entre las principales se encuentran:

* `fastapi`
* `uvicorn`
* `pydantic`
* `pydantic-settings`
* `python-jose`
* `python-multipart`

Para las pruebas:

* `pytest`
* `httpx`

### `backend/app/core/`

Contiene código compartido y configuraciones generales de la aplicación.

* `config.py` — Variables de configuración, por ejemplo `CORS_ORIGINS`.
* `logger.py` — Configuración del sistema de logging.
* `middleware.py` — Middlewares personalizados, por ejemplo el middleware de logging.
* `security.py` — Utilidades relacionadas con seguridad y autenticación.

### `backend/app/routers/`

Contiene los routers de FastAPI, donde se definen los endpoints de la API.

* `health_router.py` — Endpoints para comprobar el estado de la aplicación.
* `auth_router.py` — Endpoints relacionados con autenticación.
* `calculadora_router.py` — Endpoints principales de la calculadora.

### `backend/app/schemas/`

Contiene los modelos de **Pydantic** utilizados para validar las solicitudes y respuestas de la API.

Ejemplos:

* `auth.py`
* `calculadora.py`

### `backend/app/services/`

Contiene la lógica de negocio separada de los routers.

Por ejemplo:

* `calculadora_service.py` — Implementa la lógica utilizada por las operaciones de la calculadora.

### `backend/tests/`

Contiene las pruebas automatizadas del backend utilizando **pytest** y **httpx**.

Los principales archivos de pruebas son:

* `test_auth.py`
* `test_calculadora.py`
* `test_cors.py`
* `test_health.py`

El archivo `conftest.py` contiene configuraciones y fixtures compartidos por las pruebas.

---

## Frontend (`frontend/`)

El frontend es una aplicación desarrollada con **React + TypeScript**, creada utilizando **Create React App**.

### `frontend/package.json`

Contiene las dependencias del proyecto y los scripts principales.

Entre los scripts habituales se encuentran:

```bash
npm start
npm run build
npm test
```

El proyecto utiliza **React 19** y **TypeScript**.

### `frontend/src/`

Contiene el código fuente principal de la aplicación.

### `components/`

Contiene los componentes de la interfaz gráfica.

Entre ellos:

* `Display.tsx`
* `Historial.tsx`
* `NumberPad.tsx`
* `OperationPad.tsx`
* `StatusBar.tsx`

### `hooks/`

Contiene hooks personalizados de React.

* `useAuth.ts` — Maneja lógica relacionada con autenticación.
* `useCalculadora.ts` — Maneja el estado y lógica de la calculadora.

### `services/api.ts`

Implementa el cliente centralizado utilizado por el frontend para realizar peticiones al backend.

### `assets/`

Contiene imágenes y otros recursos estáticos utilizados por la aplicación.

Por ejemplo:

```text
assets/
└── image/
    └── mate.png
```

### `setupTests.ts`

Contiene la configuración necesaria para ejecutar pruebas del frontend utilizando **Testing Library**.

### `nginx.conf.template`

Plantilla de configuración de **Nginx** utilizada para servir el frontend en producción.

### `Dockerfile`

Define la imagen Docker utilizada para construir y ejecutar el frontend.

---

# Cómo ejecutar el proyecto

Consulta también el archivo `README.md` para conocer las instrucciones específicas del repositorio.

## 1. Ejecutar con Docker Compose

Esta es la opción recomendada para mantener un entorno reproducible.

Desde la raíz del repositorio:

```bash
docker compose build
docker compose up -d
```

Para ejecutar los tests del backend, de la misma forma que se realizan en CI:

```bash
docker compose run --rm backend pytest
```

---

## 2. Ejecutar localmente sin Docker

### Backend

Entrar a la carpeta del backend:

```bash
cd backend
```

Crear un entorno virtual:

```bash
python -m venv .venv
```

En Linux/macOS, activarlo con:

```bash
source .venv/bin/activate
```

En Windows:

```powershell
.venv\Scripts\activate
```

Instalar las dependencias:

```bash
pip install -r requirements.txt
```

Ejecutar FastAPI con Uvicorn:

```bash
uvicorn main:app --reload --host 0.0.0.0 --port 8000
```

La API estará disponible en:

```text
http://localhost:8000/api
```

La documentación Swagger estará disponible en:

```text
http://localhost:8000/api/docs
```

---

### Frontend

Entrar a la carpeta:

```bash
cd frontend
```

Instalar las dependencias:

```bash
npm install
```

Ejecutar la aplicación:

```bash
npm start
```

Por defecto, Create React App sirve la aplicación en:

```text
http://localhost:3000
```

si el puerto `3000` se encuentra disponible.
