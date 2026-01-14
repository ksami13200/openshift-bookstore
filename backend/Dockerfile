# =========================
# 1️⃣ Builder stage
# =========================
FROM node:18-bookworm-slim AS builder

WORKDIR /app

# Copy dependency manifests
COPY package.json package-lock.json ./

# Install production deps only
RUN npm ci --omit=dev --ignore-scripts && \
    npm cache clean --force

# Copy app code
COPY server.js ./

# Set ownership for distroless (UID 65532 = non-root)
RUN chown -R 65532:65532 /app


# =========================
# 2️⃣ Runtime stage (Distroless)
# =========================
FROM gcr.io/distroless/nodejs18-debian12@sha256:<digest>


WORKDIR /app

# Copy built app
COPY --from=builder /app /app

# Environment
ENV NODE_ENV=production
ENV PORT=3000

# Expose port (for documentation only)
EXPOSE 3000

# Run app with Node.js
CMD ["node", "server.js"]