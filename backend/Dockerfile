FROM node:12.16-alpine as base

RUN apk update \
    && apk add --no-cache \
        bash \
        shadow

FROM scratch as user
COPY --from=base . .

ARG HOST_UID=${HOST_UID:-4000}
ARG HOST_USER=${HOST_USER:-nodummy}

RUN [ "${HOST_UID}" == "1000" ] && \
    ( \
      usermod -l ${HOST_USER} node \
        && usermod -d /home/${HOST_USER} ${HOST_USER} \
        && groupmod -n ${HOST_USER} node \
        && mkdir -p /home/${HOST_USER} \
        && chown -R "${HOST_UID}:${HOST_UID}" /home/${HOST_USER}\
    ) || \
    [ "${HOST_USER}" == "root" ] || \
    ( \
      adduser -h /home/${HOST_USER} -D -u ${HOST_UID} ${HOST_USER} \
        && chown -R "${HOST_UID}:${HOST_UID}" /home/${HOST_USER} \
    )

WORKDIR /home/${HOST_USER}

COPY --chown="${HOST_UID}:${HOST_UID}" package*.json ./

RUN npm install --no-audit --no-optional && \
  mv package-lock.json /tmp/package-lock.json

COPY --chown="${HOST_UID}:${HOST_UID}" . ./

USER ${HOST_USER}
