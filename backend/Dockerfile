# =========================
# 1️⃣ Builder stage
# =========================
FROM node:18-bookworm-slim AS builder

# Create app directory
WORKDIR /app

# Copy dependency manifests
COPY package.json package-lock.json ./

# Install only production dependencies
RUN npm ci --omit=dev --ignore-scripts && \
    # Remove unnecessary files to reduce layer size
    npm cache clean --force

# Copy application code
COPY server.js ./

# Ensure files are readable by non-root user (UID 65532 in distroless)
RUN chown -R 65532:65532 /app


# =========================
# 2️⃣ Runtime stage (Distroless)
# =========================
FROM gcr.io/distroless/nodejs18-debian12

# Set working directory
WORKDIR /app

# Copy built app from builder (already owned by UID 65532)
COPY --from=builder --chown=65532:65532 /app /app

# Environment
ENV NODE_ENV=production
ENV PORT=3000

# Expose port (documentation only)
EXPOSE 3000

# Run the app
CMD ["server.js"]

