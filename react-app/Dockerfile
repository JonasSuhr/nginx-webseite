# ---- STAGE 1: Builder ----
FROM node:lts-alpine AS builder

WORKDIR /app

COPY package*.json ./
RUN npm ci

COPY . .
RUN npm run build

# ---- STAGE 2: Runtime ----
FROM nginx:alpine

WORKDIR /usr/share/nginx/html
RUN rm -rf ./*

COPY --from=builder /app/dist .

EXPOSE 80

HEALTHCHECK CMD curl --fail http://localhost:80 || exit 1

