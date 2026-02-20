FROM node:20-alpine AS base

FROM base AS builder
RUN apk add --no-cache libc6-compat
WORKDIR /app
RUN npm i -g turbo
COPY . .
RUN turbo prune @btm/iam --docker

FROM base AS installer
RUN apk add --no-cache libc6-compat openssl
WORKDIR /app

COPY .gitignore .gitignore
COPY --from=builder /app/out/json/ .
COPY --from=builder /app/out/package-lock.json ./package-lock.json
RUN npm ci

COPY --from=builder /app/out/full/ .
COPY turbo.json turbo.json

RUN npx turbo run build --filter=@btm/iam

FROM base AS runner
WORKDIR /app
RUN apk add --no-cache openssl

COPY --from=installer /app .

CMD ["node", "apis/iam/dist/index.js"]