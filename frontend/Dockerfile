############################
# Stage 1: Builder
############################
FROM nginx:1.25.4-alpine AS builder
# Official, verified NGINX image

# Create non-root user (UID 1001)
RUN addgroup -g 1001 appgroup && \
    adduser -D -u 1001 -G appgroup appuser

# Remove default nginx config (VERY IMPORTANT)
RUN rm -f /etc/nginx/conf.d/default.conf

# Copy custom nginx config
COPY nginx.conf /etc/nginx/nginx.conf

# Copy frontend static files
COPY html/ /usr/share/nginx/html/

# Fix ownership
RUN chown -R 1001:1001 /usr/share/nginx/html /etc/nginx

############################
# Stage 2: Runtime
############################
FROM alpine:3.19

# Install nginx only (minimal runtime)
RUN apk add --no-cache nginx && \
    addgroup -g 1001 appgroup && \
    adduser -D -u 1001 -G appgroup appuser && \
    rm -f /etc/nginx/conf.d/default.conf

# Copy ONLY what is needed
COPY --from=builder /usr/share/nginx/html /usr/share/nginx/html
COPY --from=builder /etc/nginx/nginx.conf /etc/nginx/nginx.conf

# Prepare writable directories for non-root nginx
RUN mkdir -p /var/cache/nginx /var/run /var/log/nginx /var/lib/nginx/logs && \
    chown -R 1001:1001 \
    /usr/share/nginx \
    /var/cache/nginx \
    /var/run \
    /etc/nginx \
    /var/log/nginx \
    /var/lib/nginx

# Run as non-root user
USER 1001

EXPOSE 8080

ENTRYPOINT ["nginx", "-g", "daemon off;"]
