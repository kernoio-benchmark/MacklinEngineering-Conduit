# Multi-stage Dockerfile for Node.js TypeScript application

# Build stage
FROM node:20-alpine AS builder

WORKDIR /app

# Copy package files
COPY package*.json ./

# Copy tsconfig.json for build
COPY tsconfig.json ./

# Copy all source code and config files (needed before npm ci due to prepare script)
COPY src ./src
COPY config ./config

# Install all dependencies (including devDependencies for build)
RUN npm ci

# Copy remaining files
COPY . .

# Build the application
RUN npm run build

# Production stage - using ts-node as per npm start
FROM node:20-alpine AS production

WORKDIR /app

# Copy package files
COPY package*.json ./

# Copy tsconfig.json from builder
COPY --from=builder /app/tsconfig.json ./

# Install all dependencies (ts-node requires TypeScript and other dev deps)
RUN npm ci --ignore-scripts && \
    npm cache clean --force

# Copy source files
COPY src ./src
COPY config ./config

# Copy swagger.yaml from builder
COPY --from=builder /app/swagger.yaml ./swagger.yaml

# Set NODE_ENV to production
ENV NODE_ENV=production

# Expose port (common default for Node.js apps)
EXPOSE 3000

# Run using npm start as documented
CMD ["npm", "start"]
