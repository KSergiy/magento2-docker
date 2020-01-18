FROM nginx:1.17

RUN groupadd -g 1000 app \
    && useradd -g 1000 -u 1000 -d /var/www -s /bin/bash app
RUN touch /var/run/nginx.pid
RUN mkdir /sock

RUN apt-get update && apt-get install -y openssl \
    nano \
    mc

RUN echo 'alias ll="ls -al"' >> ~/.bashrc

#RUN mkdir /etc/nginx/certs \
#    && echo -e "\n\n\n\n\n\n\n" | openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/nginx/certs/nginx.key -out /etc/nginx/certs/nginx.crt

COPY ./conf/certs/nginx.key /etc/nginx/certs/
COPY ./conf/certs/nginx.crt /etc/nginx/certs/
COPY ./conf/nginx.conf /etc/nginx/
COPY ./conf/default.conf /etc/nginx/conf.d/

# RUN mkdir -p /etc/nginx/html /var/www/html \
#    && chown -R app:app /etc/nginx /var/www /var/cache/nginx /var/run/nginx.pid /sock

RUN mkdir -p /etc/nginx/html /var/cache/nginx /var/cache/nginx/client_temp \
    && chown -R app:app /etc/nginx /var/cache/nginx /var/cache/nginx/client_temp /var/run/nginx.pid /sock

EXPOSE 8080 8443

USER app:app

VOLUME /var/www

WORKDIR /var/www/html