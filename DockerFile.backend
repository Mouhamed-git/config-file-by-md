FROM node:13

# Create workdir app
WORKDIR /usr/src/app

# Copy package.json package-lock.json on current container directory
COPY package*.json ./

# Install dependencies
RUN npm ci

# Copy all others files needed
COPY . .

# Exposed port 8080
EXPOSE 8080

# Run server when container is up
CMD [ "npm", "run", "prod" ]
