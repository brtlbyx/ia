FROM nginx:1.17.8

COPY etc/nginx/snakeoil/ /etc/nginx/snakeoil/

# Setze die richtigen Dateirechte direkt im Image
RUN chmod 640 /etc/nginx/snakeoil/ssl-cert-snakeoil.key && \
    chmod 644 /etc/nginx/snakeoil/ssl-cert-snakeoil.pem && \
    chown root:root /etc/nginx/snakeoil/ssl-cert-snakeoil.*

# Erstelle das Verzeichnis für Nginx PID und Logs
RUN mkdir -p /var/run/nginx && rm -rf /var/log/nginx/*

# Starte Nginx mit automatischem Reload alle 6 Stunden
CMD ["sh", "-c", "while :; do sleep 6h & wait $${!}; nginx -s reload; done & nginx -g 'daemon off;'"]