############################
# 1️⃣ Builder stage
############################
FROM node:18-bookworm-slim AS builder

# Set working directory
WORKDIR /app

# Copy only dependency manifests first (better layer caching)
COPY package.json package-lock.json ./

# Install ONLY production dependencies
RUN npm ci --omit=dev

# Copy application source
COPY server.js ./

# -------------------------
# Optional hardening
# -------------------------
# Remove npm cache to reduce size
RUN npm cache clean --force


############################
# 2️⃣ Runtime stage (Distroless)
############################
FROM gcr.io/distroless/nodejs18-debian12
# Set working directory
WORKDIR /app
# ... other Dockerfile instructions ...
RUN apt-get update && apt-get upgrade -y libc6 && apt-get clean && rm -rf /var/lib/apt/lists/*
# ... rest of your Dockerfile ...

# Copy node_modules and app from builder
COPY --from=builder /app /app

# Use non-root user (distroless provides uid 65532)
USER nonroot

# Environment variables
ENV NODE_ENV=production
ENV PORT=3000

# Expose application port
EXPOSE 3000

# Start application
CMD ["server.js"]
