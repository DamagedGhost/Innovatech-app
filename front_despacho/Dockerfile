# --- Stage 1: Construcción (Build) ---
FROM node:18-alpine AS build
WORKDIR /app

# Copiamos dependencias primero para aprovechar la caché de Docker
COPY package*.json ./

# Instalamos TODAS las dependencias (necesitamos Vite para compilar)
RUN npm ci

# Copiamos el resto del código fuente
COPY . .

# Compilamos la aplicación de React. Esto creará la carpeta /dist
RUN npm run build


# --- Stage 2: Imagen final para producción (Nginx) ---
# Usamos Nginx Alpine, que pesa apenas unos 10-15MB
FROM nginx:alpine AS production

# Copiamos SOLO los archivos estáticos compilados desde la etapa anterior
# Descartamos todo el código fuente original y node_modules (capas limpias)
COPY --from=build /app/dist /usr/share/nginx/html

COPY nginx.conf /etc/nginx/conf.d/default.conf

# Exponemos el puerto estándar de Nginx
EXPOSE 80

# Creamos un usuario no root para mejorar la seguridad
RUN addgroup -S nginx && adduser -S nginx -G nginx

# Cambiamos a ese usuario para ejecutar Nginx
USER nginx:nginx

# Comando para iniciar Nginx en primer plano
CMD ["nginx", "-g", "daemon off;"]