# ----------------------------------------------------
# Multi-stage Dockerfile for Academic Management Backend
# ----------------------------------------------------

# Stage 1: Build stage
FROM node:20-bookworm-slim AS builder
WORKDIR /app

# Install OpenSSL required by Prisma
RUN apt-get update && apt-get install -y --no-install-recommends openssl ca-certificates \
    && rm -rf /var/lib/apt/lists/*

# Install all dependencies (including devDependencies)
COPY package*.json ./
COPY prisma ./prisma/
RUN npm ci

# Copy source code and build
COPY tsconfig.json ./
COPY src ./src
RUN npx prisma generate
RUN npm run build

# Stage 2: Production runner
FROM node:20-bookworm-slim AS runner
WORKDIR /app

ENV NODE_ENV=production
ENV PORT=5000
ENV HOST=0.0.0.0

# Install OpenSSL and curl for container health check
RUN apt-get update && apt-get install -y --no-install-recommends openssl curl ca-certificates \
    && rm -rf /var/lib/apt/lists/*

# Install production dependencies only
COPY package*.json ./
COPY prisma ./prisma/
RUN npm ci --omit=dev

# Copy compiled files and generated prisma artifacts from builder
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules/.prisma ./node_modules/.prisma
COPY --from=builder /app/node_modules/@prisma ./node_modules/@prisma

# Create unprivileged user
RUN useradd -m -u 1001 nodejs && chown -R nodejs:nodejs /app
USER nodejs

EXPOSE 5000

HEALTHCHECK --interval=15s --timeout=5s --start-period=20s --retries=3 \
  CMD curl -f http://localhost:5000/health || exit 1

CMD ["node", "dist/server.js"]
