FROM mysql:5.7

ARG USER
ARG UID 

# Volume Permission Patch
RUN useradd -ms /bin/bash $USER || true
RUN usermod -aG root,mysql $USER
RUN chown -R $USER:root ./var/log && chown -R $USER:root ./var/run && chown -R $USER:root ./usr/share
RUN chmod -R 777 ./var/log && chmod -R 777 ./var/run && chmod -R 777 ./usr/share
USER $USER

WORKDIR /media