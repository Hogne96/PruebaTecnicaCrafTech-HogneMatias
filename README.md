# DEVOPS-INTERVIEW-ULTIMATE

Este repositorio contiene una aplicación full-stack compuesta por:
- **Backend:** API desarrollada en Django (con Django REST Framework y autenticación JWT).
- **Frontend:** Aplicación en React.js.

La solución está dockerizada, orquestada mediante un único archivo `docker-compose.yml`, lo que facilita su despliegue en entornos locales y en la nube.

---

## Tabla de Contenidos

- [Estructura del Proyecto](#estructura-del-proyecto)
- [Requisitos](#requisitos)
- [Instrucciones para Compilar y Desplegar Localmente](#despliegue-local)
- [Instrucciones para Desplegar en la Nube (AWS o GCP)](#despliegue-en-la-nube)
- [Detalles de Configuración](#detalles-de-configuración)
- [Notas y Justificación](#notas-y-justificación)

---

## Estructura del Proyecto

DEVOPS-INTERVIEW-ULTIMATE/
├── backend/              # Código de Django
│   ├── api (carpeta)
│   ├── core (carpeta)
│   ├── .env
│   ├── .env.postgres
│   ├── .gitignore
│   ├── Dockerfile
│   ├── entrypoint.sh
│   ├── manage.py
│   ├── package.json
│   ├── pytest.ini
│   ├── README.md
│   └── requirements.txt
├── frontend/             # Código de React
│   ├── public (carpeta)
│   ├── src (carpeta)
│   ├── .gitignore
│   ├── Dockerfile
│   ├── package-lock.json
│   ├── package.json
│   └── README.md
├── docker-compose.yml    # Orquestación de todos los servicios
└── README.md             # Instrucciones detalladas para compilar y desplegar


---

## Requisitos

- **Docker** y **Docker Compose** instalados en el equipo.
- Acceso a Internet para descargar imágenes base y dependencias.
- Para despliegues en la nube, acceso a una cuenta en AWS o GCP y conocimientos básicos para configurar instancias o servicios gestionados.

---

## Despliegue Local

### 1. Clonar el repositorio

```bash
git clone https://github.com/tu_usuario/DEVOPS-INTERVIEW-ULTIMATE.git
cd DEVOPS-INTERVIEW-ULTIMATE

### 2. Configurar variables de entorno del Backend

En el directorio backend/, asegúrate de contar con los siguientes archivos:

.env.postgres (ya incluido) con contenido similar a:
DATABASE=postgres
POSTGRES_USER=user
POSTGRES_PASSWORD=password
POSTGRES_DB=core

.env con las variables requeridas por Django, por ejemplo:
SQL_ENGINE=django.db.backends.postgresql
SQL_DATABASE=postgres
SQL_USER=user
SQL_PASSWORD=password
SQL_HOST=db
SQL_PORT=5432

Nota: La aplicación ha sido configurada para obtener los datos de la base de datos a través de estas variables (revisa el código fuente en settings.py para confirmar las variables utilizadas).

