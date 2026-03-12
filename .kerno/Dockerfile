# Build stage
FROM node:20-alpine AS builder

WORKDIR /app

# Copy package files
COPY package*.json ./

# Install all dependencies (including devDependencies for build), ignoring prepare script
RUN npm ci --ignore-scripts

# Copy source code and configuration files
COPY . .

# Build the application
RUN npm run build

# Production stage
FROM node:20-alpine AS production

WORKDIR /app

# Copy package files
COPY package*.json ./

# Install only production dependencies, ignoring scripts to avoid prepare hook
RUN npm ci --only=production --ignore-scripts && \
    npm cache clean --force

# Copy built application from builder stage
COPY --from=builder /app/build ./build

# Copy swagger.yaml if it exists
COPY swagger.yaml ./swagger.yaml

# Set NODE_ENV to production
ENV NODE_ENV=production

# Expose port (common default, adjust if needed)
EXPOSE 3000

# Run the compiled application
CMD ["node", "build/src/app.js"]
