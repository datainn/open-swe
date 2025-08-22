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
# Inject public runtime config at build time so client bundle has URLs
ARG NEXT_PUBLIC_API_URL
ARG LANGGRAPH_API_URL
ARG NEXT_PUBLIC_GITHUB_APP_CLIENT_ID
ENV NODE_ENV=production
ENV NEXT_TELEMETRY_DISABLED=1
ENV NEXT_PUBLIC_API_URL=${NEXT_PUBLIC_API_URL}
ENV LANGGRAPH_API_URL=${LANGGRAPH_API_URL}
ENV NEXT_PUBLIC_GITHUB_APP_CLIENT_ID=${NEXT_PUBLIC_GITHUB_APP_CLIENT_ID}
RUN yarn build

EXPOSE 8080

ENV HOSTNAME=0.0.0.0
ENV PORT=8080

# Run the web app production server
CMD ["yarn", "start"]


