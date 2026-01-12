# ---- Builder Stage ----
FROM node:20-alpine AS builder

WORKDIR /app

# Copy ONLY dependency manifests (enables caching)
COPY package.json package-lock.json ./app/

# Install production dependencies only
RUN npm ci --omit=dev --no-fund --no-audit --loglevel=error && \
    npm cache clean --force

# ---- Runtime Stage ----
FROM node:20-alpine AS runtime

# Create non-root user
RUN addgroup -g 1001 -S appgroup && \
    adduser -S appuser -u 1001 -G appgroup

WORKDIR /app

# Copy production node_modules from builder
COPY --from=builder --chown=appuser:appgroup /app/node_modules ./node_modules

# Copy ONLY your application entry point (e.g., server.js)
COPY --chown=appuser:appgroup server.js ./

# Drop privileges
USER appuser

EXPOSE 3000

CMD ["node", "server.js"]