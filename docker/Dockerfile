
# Stage 1 - Build environment
FROM node:22-alpine AS build

RUN apk add --no-cache python3 make g++ git

WORKDIR /usr/src/app

COPY .npmrc ./
COPY package*.json ./

RUN npm ci

COPY . .

# Build iptvNT
RUN npm run build:web

# Stage 2 - Production environment
FROM nginx:stable-alpine

# Copy compiled frontend
COPY --from=build /usr/src/app/dist/browser /usr/share/nginx/html

# Copy Nginx configuration
COPY --from=build /usr/src/app/docker/nginx.conf /etc/nginx/conf.d/default.conf

# Nginx listens on port 80
EXPOSE 80

# Configure backend URL when container starts
CMD ["sh", "-c", "if [ -z \"$BACKEND_URL\" ]; then echo 'ERROR: BACKEND_URL is not configured'; exit 1; fi; sed -i \"s|PLACEHOLDER_BACKEND_URL|${BACKEND_URL}|g\" /usr/share/nginx/html/assets/config.json && exec nginx -g 'daemon off;'"]
