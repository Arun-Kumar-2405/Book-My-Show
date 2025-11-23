###############  Stage 1 : Build React App  ###############
FROM node:18 AS build

WORKDIR /app

# Copy dependency files
COPY package.json package-lock.json ./

# Fix OpenSSL issue for React build
ENV NODE_OPTIONS=--openssl-legacy-provider

# Install dependencies
RUN npm install --legacy-peer-deps

# Copy all project files
COPY . .

# Build production build
RUN npm run build


###############  Stage 2 : Nginx Web Server ###############
FROM nginx:1.25

# Copy build output from previous stage
COPY --from=build /app/build /usr/share/nginx/html

# Expose port
EXPOSE 80

# Start Nginx
CMD ["nginx", "-g", "daemon off;"]

