# Build stage
FROM node:18-alpine AS builder
WORKDIR /app

# Copy package files and source code
COPY package.json package-lock.json tsconfig.json ./
COPY src ./src
COPY ../frontend ./frontend

# Install dependencies and build the application
RUN npm ci
RUN npm run build

# Runtime stage
FROM node:18-alpine AS runtime
WORKDIR /app

# Copy built files and dependencies from the builder stage
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/frontend ./frontend

# Set environment variables and expose the application port
ENV PORT=8080
EXPOSE 8080
CMD ["node", "dist/main.js"]
