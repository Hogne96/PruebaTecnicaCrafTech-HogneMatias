# backend/Dockerfile
FROM python:3.9-slim

# Evitar buffering en los logs
ENV PYTHONUNBUFFERED=1

# Actualizar e instalar dependencias del sistema necesarias para psycopg2
RUN apt-get update && apt-get install -y gcc libpq-dev && apt-get clean

# Directorio de trabajo
WORKDIR /app

# Instalar dependencias
COPY requirements.txt .
RUN pip install --upgrade pip && pip install -r requirements.txt

# Copiar el entrypoint de forma explícita
COPY entrypoint.sh /app/entrypoint.sh

# Dar permisos de ejecución al entrypoint
RUN chmod +x entrypoint.sh

# Exponer el puerto donde Gunicorn atenderá
EXPOSE 8000

# Usar el entrypoint para esperar a la DB y aplicar migraciones
CMD ["/app/entrypoint.sh"]
