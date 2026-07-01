FROM IMAGE=debian:stable-slim

USER root
ENV DEBIAN_FRONTEND=noninteractive

RUN apt update
RUN apt install -y \
        curl \
        openssl \
        procps \
        python3 \
        python3-gpg
RUN rm -rf /var/lib/apt/lists/*

ARG USERACCOUNT
ARG GROUPACCOUNT
ARG UID
ARG GID

# Create group if missing
RUN getent group "${GID}" || \
    groupadd -g "${GID}" "${GROUPACCOUNT}"

# Create user if missing
RUN getent passwd "${UID}" || \
    useradd \
        -m \
        -u "${UID}" \
        -g "${GID}" \
        "${USERACCOUNT}"

# Generate random password and save it
ARG PASSWD_OUTPUT="/var/log/user-${USERACCOUNT}.password"
RUN PASSWD="$(openssl rand -base64 16)" && \
    echo "${USERACCOUNT}:$PASSWD" | chpasswd && \
    echo "The password for the user ${USERACCOUNT} is: $PASSWD" \
    > "${PASSWD_OUTPUT}"
RUN chown ${UID}:${GID} "${PASSWD_OUTPUT}"

RUN mkdir -p "/home/${USERACCOUNT}/Dropbox"
RUN chown ${UID}:${GID} "/home/${USERACCOUNT}/Dropbox"
RUN mkdir -p "/home/${USERACCOUNT}/.dropbox"
RUN chown ${UID}:${GID} "/home/${USERACCOUNT}/.dropbox"

USER ${USERACCOUNT}

RUN curl -L \
    https://linux.dropbox.com/packages/dropbox.py \
    -o /home/"${USERACCOUNT}"/dropbox.py

RUN echo y | python3 ~/dropbox.py update
ENV PASSWD_OUTPUT=${PASSWD_OUTPUT}

CMD sh -c "cat $PASSWD_OUTPUT && ~/.dropbox-dist/dropboxd"