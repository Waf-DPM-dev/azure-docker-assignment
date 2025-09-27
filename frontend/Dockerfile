# syntax=docker/dockerfile:1

########## BUILD STAGE ##########
FROM node:20-alpine AS build
WORKDIR /app

# Install deps (supports both with/without lockfile for reproducible CI)
COPY package.json package-lock.json* ./
RUN if [ -f package-lock.json ]; then npm ci; else npm install; fi

# Copy source and build the React app
COPY . .
# React reads REACT_APP_* at build time and bakes it into the JS bundle
ARG REACT_APP_API_URL
ENV REACT_APP_API_URL=${REACT_APP_API_URL:-http://backend:3001}
RUN npm run build


########## RUNTIME STAGE ##########
FROM nginx:alpine
# Copy the built static files into NGINX's web root
COPY --from=build /app/build /usr/share/nginx/html

# Document container port (host mapping is done in compose)
EXPOSE 80

# Start NGINX in foreground
CMD ["nginx", "-g", "daemon off;"]
