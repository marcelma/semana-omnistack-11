FROM node:12.16-alpine

RUN apk update \
    && apk add --no-cache \
        bash \
        shadow \
        python3 \
        procps \
        git

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

RUN npm install -g expo-cli

USER ${HOST_USER}

RUN npm install @react-navigation/native

RUN expo install \
    react-native-gesture-handler \
    react-native-reanimated \
    react-native-screens \
    react-native-safe-area-context \
    @react-native-community/masked-view \
    expo-constants \
    expo-mail-composer

RUN npm install --no-audit --no-optional

COPY --chown="${HOST_UID}:${HOST_UID}" . .
