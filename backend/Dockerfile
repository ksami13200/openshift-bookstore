# =========================
# 1️⃣ Builder stage
# =========================
FROM node:18-bookworm-slim AS builder

# Create custom user 'Khaled'
RUN groupadd -g 10001 Khaled && \
    useradd -u 10001 -g Khaled -m Khaled

WORKDIR /app

COPY package.json package-lock.json ./
RUN npm ci --omit=dev --ignore-scripts && npm cache clean --force

COPY server.js ./

# =========================
# 2️⃣ Runtime stage (Distroless)
# =========================
# Using a specific versioned tag instead of 'latest'
FROM gcr.io/distroless/nodejs18-debian12:20

WORKDIR /app

# Copy user identity from builder
COPY --from=builder /etc/passwd /etc/passwd
COPY --from=builder /etc/group /etc/group

# Copy app and set ownership to Khaled
COPY --from=builder --chown=Khaled:Khaled /app /app

# Switch to the custom user
USER Khaled

ENV NODE_ENV=production
ENV PORT=3000

EXPOSE 3000

# Execute the app
CMD ["server.js"]
