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
- [Detalles de Configuración](#detalles-de-configuración)
- [Notas y Justificación](#notas-y-justificación)

---

## Estructura del Proyecto
```
DEVOPS-INTERVIEW-ULTIMATE/
├── .github/                                            
│   ├── workflows
│   │   ├── deploy-nginx.yml                            # Pipeline para actualizar imagen al cambiar el index.html
├── backend/                                            # Código de Django
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
├── frontend/                                           # Código de React
│   ├── public (carpeta)
│   ├── src (carpeta)
│   ├── .gitignore
│   ├── Dockerfile
│   ├── package-lock.json
│   ├── package.json
│   └── README.md
├── Descripción de la Arquitectura y Justificación.pdf
├── docker-compose.nginx.yml                            # orquestacion de actualizacion de imagen al cambiar el index.html
├── docker-compose.yml                                  # Orquestación de todos los servicios
└── README.md                                           # Instrucciones detalladas para compilar y desplegar
```

---

## Requisitos

- **Docker** y **Docker Compose** instalados en el equipo.
- Acceso a Internet para descargar imágenes base y dependencias.
- Para despliegues en la nube: una cuenta en AWS (o GCP) y conocimientos básicos para provisionar instancias y configurar redes.
- Para producción, se recomienda usar almacenamiento persistente (volúmenes EBS) o servicios gestionados (RDS para la base de datos).

---

## Despliegue Local

### 1. Clonar el repositorio

```
bash
git clone https://github.com/Hogne96/PruebaTecnicaCrafTech-HogneMatias.git
cd PruebaTecnicaCrafTech-HogneMatias
```


### 2. Configurar variables de entorno del Backend

En el directorio backend/, asegúrate de contar con los siguientes archivos:

.env.postgres (ya incluido) con contenido similar a:
```
DATABASE=postgres
POSTGRES_USER=user
POSTGRES_PASSWORD=password
POSTGRES_DB=core
```

.env con las variables requeridas por Django, por ejemplo:
```
SQL_ENGINE=django.db.backends.postgresql
SQL_DATABASE=postgres
SQL_USER=user
SQL_PASSWORD=password
SQL_HOST=db
SQL_PORT=5432
```

Nota: La aplicación ha sido configurada para obtener los datos de la base de datos a través de estas variables (revisa el código fuente en settings.py para confirmar las variables utilizadas).

### 3. Construir y levantar los contenedores

Desde la raíz del proyecto, ejecuta:
```
docker-compose up --build
```

Esto realizará las siguientes acciones:

- Construirá la imagen del backend usando backend/Dockerfile.
- Construirá la imagen del frontend usando frontend/Dockerfile.
- Levantará el servicio de PostgreSQL con la configuración del archivo .env.postgres.
- Orquestará los tres servicios mediante el archivo docker-compose.yml.

### 4. Verificar el funcionamiento

- Backend (Django API): http://localhost:8000
- Frontend (React): http://localhost:3000

## Detalles de Configuración

- backend/Dockerfile:
    - Usa la imagen python:3.9-slim.
    - Instala dependencias del sistema (gcc, libpq-dev) y las dependencias Python definidas en requirements.txt.
    - Copia el script entrypoint.sh que se encarga de esperar a la base de datos y aplicar migraciones.
    - Expone el puerto 8000 y define el comando de inicio con Gunicorn.

- frontend/Dockerfile:
    - Utiliza una etapa de build basada en node:16 para construir la aplicación React.
    - En una segunda etapa, utiliza nginx:alpine para servir los archivos estáticos generados (copiándolos al directorio por defecto de Nginx).
    - Expone el puerto 80.

- docker-compose.yml:

    - Define tres servicios: db (Postgres), backend (Django) y frontend (React servida por Nginx).
    - Se utilizan volúmenes para persistencia en la base de datos y para compartir el código (estos últimos son útiles en desarrollo, pero se deben ajustar en producción).


## Notas y Justificación

### Elección de Docker Compose:
Se optó por dockerizar la aplicación full-stack y orquestarla mediante Docker Compose debido a las siguientes ventajas:
- Simplicidad y Reproducibilidad:
        Con un único archivo de orquestación (docker-compose.yml) se coordinan todos los servicios (base de datos, backend y frontend). Esto facilita el despliegue, ya que se dispone de una configuración centralizada.
- Separación de Responsabilidades:
        Cada componente (backend, frontend y base de datos) se construye y se ejecuta en su contenedor, permitiendo gestionar de forma independiente sus dependencias y actualizaciones.
- Facilidad para Automatización CI/CD:
        Con pipelines en GitHub Actions se automatiza la construcción y despliegue de nuevas versiones, garantizando que cada actualización se publique (por ejemplo, en Docker Hub) y se refleje inmediatamente en el entorno mediante el pipeline deploy-nginx.yml.

### Detalle de los Archivos Principales

#### Dockerfile del Backend
Define cómo construir la imagen del API Django, instalando dependencias y configurando el entrypoint para esperar la base de datos, aplicar migraciones y lanzar el servidor.

#### Dockerfile del Frontend
Realiza un build en dos etapas para la aplicación React: primero compila el código con Node y luego crea una imagen basada en Nginx que sirve los archivos estáticos.

#### docker-compose.yml
Orquesta los servicios para el entorno de desarrollo (base de datos, backend y frontend), utilizando volúmenes y montajes de código para facilitar la iteración.

#### docker-compose.nginx.yml
Específico para desplegar una imagen personalizada de Nginx que sirva el contenido estático actualizado (por ejemplo, tras modificar el index.html); se utiliza en el pipeline de despliegue.

#### Pipeline deploy-nginx.yml
Workflow de GitHub Actions que se activa al cambiar el index.html; construye, publica (opcionalmente) y despliega automáticamente la imagen de Nginx mediante Docker Compose.