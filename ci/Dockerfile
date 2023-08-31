FROM node:20-slim

RUN apt update && apt install -y chromium fonts-noto-cjk

ENV CHROME_PATH /usr/bin/chromium-browser

COPY package.json package-lock.json .
RUN npm ci
