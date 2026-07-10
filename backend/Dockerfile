# backend/Dockerfile
FROM node:20-alpine AS frontend-builder
WORKDIR /app/frontend
COPY frontend/package*.json ./
RUN npm ci
COPY frontend/ ./
RUN npm run build

FROM golang:1.22-alpine AS backend-builder
WORKDIR /app/backend
COPY backend/go.* ./
RUN go mod download
COPY backend/ ./
COPY --from=frontend-builder /app/frontend/out ./out
RUN go build -ldflags="-s -w" -o gateway main.go

FROM alpine:3.19
RUN apk add --no-cache ca-certificates
COPY --from=backend-builder /app/backend/gateway /gateway
EXPOSE 8085
ENTRYPOINT ["/gateway"]