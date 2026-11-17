
# Stage 1 - Build environment
FROM node:22-alpine AS build

RUN apk add --no-cache python3 make g++ git

WORKDIR /usr/src/app

COPY .npmrc ./
COPY package*.json ./

RUN npm ci

COPY . .

RUN npm run build:web

# Stage 2 - Production environment
FROM nginx:stable-alpine

COPY --from=build /usr/src/app/dist/browser /usr/share/nginx/html
COPY --from=build /usr/src/app/docker/nginx.conf /etc/nginx/conf.d/default.conf

# Default backend URL
ENV BACKEND_URL=http://localhost:3333

# Document the Nginx port
EXPOSE 80

# Replace backend URL and start Nginx
CMD ["sh", "-c", "sed -i \"s|PLACEHOLDER_BACKEND_URL|${BACKEND_URL}|g\" /usr/share/nginx/html/assets/config.json && exec nginx -g 'daemon off;'"]
