# Use official Node image as the base image
FROM node:20

# Set the working directory
WORKDIR /usr/src/app

# Copy package.json and package-lock.json
COPY package*.json ./

# Install all the dependencies
RUN npm install

# Add the source code to app
COPY . .

# Install the Angular CLI
# Note: You may want to specify a version compatible with your project
RUN npm install -g @angular/cli

# Expose the port the app runs on
EXPOSE 4200

# Command to run the app
CMD ["ng", "serve", "--host", "0.0.0.0"]
