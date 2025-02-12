# frontend/Dockerfile

# Etapa 1: Construcción de la aplicación
FROM node:16 AS build
WORKDIR /app
COPY package.json package-lock.json* ./
RUN npm install
COPY . .
RUN npm run build

# Etapa 2: Servir la aplicación estática con Nginx
FROM nginx:alpine

# Copiar el build de React al directorio que Nginx sirve por defecto
COPY --from=build /app/build /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
