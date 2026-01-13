# =========================
# Stage 1: Builder
# =========================
FROM alpine:3.20 AS builder

WORKDIR /app

# Copy frontend assets
COPY html/* .

# Copy nginx configuration
COPY nginx.conf /nginx.conf
# Validate nginx configuration
RUN apk add --no-cache nginx && \
    nginx -t -c /nginx.conf && \
    apk del nginx


# =========================
# Stage 2: Runtime (Distroless)
# =========================
FROM docker.io/library/nginx:1.25-alpine AS runtime

# Copy static files
COPY --from=builder /app /usr/share/nginx/html

# Override default nginx config
COPY --from=builder /nginx.conf /etc/nginx/nginx.conf

EXPOSE 8080

# The nginx:alpine image already creates an 'nginx' user
USER nginx
