# syntax=docker/dockerfile:1
FROM node:18-bullseye

# Enable Corepack to use the pinned Yarn version
RUN corepack enable

# Install dependencies from the monorepo root for caching efficiency
WORKDIR /app

# Copy only manifests first to maximize Docker layer caching
COPY package.json yarn.lock turbo.json ./
COPY apps/open-swe/package.json apps/open-swe/package.json
COPY apps/web/package.json apps/web/package.json
COPY packages/shared/package.json packages/shared/package.json

COPY . .

# Install all workspace dependencies
RUN yarn install --immutable

# Build Next.js for production to reduce runtime memory and startup cost
WORKDIR /app/apps/web
ENV NODE_ENV=production
ENV NEXT_TELEMETRY_DISABLED=1
RUN yarn build

EXPOSE 8080

ENV HOSTNAME=0.0.0.0
ENV PORT=8080

# Run the web app production server
CMD ["yarn", "start"]


