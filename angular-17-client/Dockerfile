# Build stage
FROM node:20 AS build

# Set the working directory for the build stage
WORKDIR /app

# Copy package.json and package-lock.json (if available)
COPY angular-17-client/package*.json ./

# Install dependencies
RUN npm ci

# Copy the rest of the application code
COPY angular-17-client/ ./

# Build the Angular app
RUN npm run build 

# Run stage
FROM nginx:alpine

# Remove default nginx config
RUN rm /etc/nginx/conf.d/default.conf

# Copy the build output to replace the default nginx contents
COPY --from=build /app/dist/angular-17-crud/browser /usr/share/nginx/html

# Copy a custom nginx configuration
# Note: Make sure you have an nginx.conf file in the angular-17-client directory
COPY angular-17-client/nginx.conf /etc/nginx/conf.d/default.conf

# Debug: List contents of nginx html directory
RUN echo "Contents of /usr/share/nginx/html:" && ls -la /usr/share/nginx/html

# Install curl for health check
RUN apk add --no-cache curl

# Add a health check
HEALTHCHECK --interval=30s --timeout=3s \
  CMD curl -f http://localhost/ || exit 1

# Expose port 80
EXPOSE 80

# Start Nginx server
CMD ["nginx", "-g", "daemon off;"]
