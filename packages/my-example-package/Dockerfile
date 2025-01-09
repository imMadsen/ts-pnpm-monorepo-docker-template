# Base image with pnpm setup
FROM node:20-slim AS base
ENV PNPM_HOME="/pnpm"
ENV PATH="$PNPM_HOME:$PATH"
RUN corepack enable

# Build stage
FROM base AS build
WORKDIR /usr/src/app

# Copy workspace files including lock file
COPY pnpm-workspace.yaml package.json ./
COPY packages/my-example-package packages/my-example-package

# Install dependencies and build
RUN --mount=type=cache,id=pnpm,target=/pnpm/store pnpm install
RUN pnpm --filter my-example-package build

# Production stage
FROM base AS production
WORKDIR /app

# Copy package.json and built files
COPY --from=build /usr/src/app/packages/my-example-package/package.json ./
COPY --from=build /usr/src/app/packages/my-example-package/dist ./dist

# Install production dependencies only
RUN --mount=type=cache,id=pnpm,target=/pnpm/store pnpm install --prod

# Adjust this port as needed for your specific package
EXPOSE 8000

CMD [ "pnpm", "start" ]