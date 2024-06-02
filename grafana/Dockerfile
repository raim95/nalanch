FROM grafana/grafana-oss
RUN mkdir /var/lib/grafana/dashboards
# Скачиваем дашборд
RUN wget -O- https://grafana.com/api/dashboards/1860/revisions/37/download > /var/lib/grafana/dashboards/node_exporter.json
# Копируем файл для добавления дашборда
COPY node_exporter.yaml /etc/grafana/provisioning/dashboards/
# Копируем файл для добавления источника данных
COPY prometheus.yaml /etc/grafana/provisioning/datasources/
