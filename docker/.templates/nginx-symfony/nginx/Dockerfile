FROM nginx:1.25.1-alpine AS base

RUN rm /etc/nginx/conf.d/default.conf
COPY conf/default.conf /etc/nginx/conf.d/default.conf

FROM base AS dev

FROM base AS prod