FROM node:20 AS builder

RUN npm install -g gulp-cli@2.3.0

WORKDIR /app

# Install deps first for better layer caching
COPY package*.json ./

ENV PUPPETEER_SKIP_DOWNLOAD=true

RUN npm install

# Copy the rest of the project
COPY . .

# --- Debug block: confirms what actually landed in the image ---
RUN echo "----- /app contents -----" && ls -la /app
RUN echo "----- looking for gulpfile -----" && \
    find /app -maxdepth 2 -iname "gulpfile*" || true
RUN npx gulp --version

# --- Fail fast with a clear message if gulpfile is missing ---
RUN test -f /app/gulpfile.js || test -f /app/gulpfile.mjs || test -f /app/gulpfile.babel.js || \
    (echo "ERROR: No gulpfile found in /app. Check .dockerignore and file location." && exit 1)

RUN npx gulp generic


FROM node:20-slim AS runner

WORKDIR /app

RUN npm install -g http-server

COPY --from=builder /app/build/generic ./build/generic

EXPOSE 8888

CMD ["http-server", "build/generic", "-p", "8888", "-c-1", "--mime-type", "application/javascript:.mjs"]