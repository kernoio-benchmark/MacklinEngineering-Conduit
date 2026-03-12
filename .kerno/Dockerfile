# Multi-stage Dockerfile for Node.js/TypeScript application

# Build stage
FROM node:18-alpine AS builder

WORKDIR /app

# Copy package files
COPY package*.json ./

# Copy TypeScript configuration
COPY tsconfig.json ./

# Install all dependencies (including devDependencies for build), ignoring prepare script
RUN npm ci --ignore-scripts

# Copy source code and configuration files
COPY . .

# Compile TypeScript to JavaScript
RUN npm run compile

# Production stage
FROM node:18-alpine AS production

WORKDIR /app

# Copy package files
COPY package*.json ./

# Install only production dependencies, ignoring scripts to avoid prepare hook
RUN npm ci --only=production --ignore-scripts && \
    npm cache clean --force

# Copy compiled build artifacts from builder stage
COPY --from=builder /app/build ./build

# Copy swagger.yaml (required for Swagger UI)
COPY swagger.yaml ./

# Expose port (default Node.js port, adjust if needed)
EXPOSE 3000

# Set NODE_ENV to production
ENV NODE_ENV=production

# Run the application using the compiled JavaScript
CMD ["node", "build/src/app.js"]
