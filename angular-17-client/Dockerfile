# Use official Node image as the base image
FROM node:20

# Set the working directory
WORKDIR /usr/src/app

# Copy package.json and package-lock.json from the correct directory
COPY angular-17-client/package*.json ./

# Install all the dependencies
RUN npm install

# Copy the rest of the application code
COPY angular-17-client/ ./

# Install the Angular CLI
RUN npm install -g @angular/cli

# Expose the port the app runs on
EXPOSE 4200

# Command to run the app
CMD ["ng", "serve", "--host", "0.0.0.0"]

