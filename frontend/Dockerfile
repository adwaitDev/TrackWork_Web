# ---------- Stage 1: Build the static files ----------
FROM node:20-alpine AS build
WORKDIR /app

# Copy only package files first to cache the npm install layer
COPY package*.json ./
RUN npm ci

# Now copy the rest of the source
COPY . .

# Vite bakes env vars into the static bundle AT BUILD TIME (unlike Spring Boot,
# which reads env vars at runtime). So we pass it in as a build arg here.
ARG VITE_API_BASE_URL
ENV VITE_API_BASE_URL=$VITE_API_BASE_URL
RUN npm run build

# ---------- Stage 2: Serve with nginx ----------
FROM nginx:alpine
COPY --from=build /app/dist /usr/share/nginx/html
COPY nginx.conf /etc/nginx/conf.d/default.conf

EXPOSE 5000
CMD ["nginx", "-g", "daemon off;"]
