## Тестовое задание DevOps

### Структура проекта
```
├── Vagrantfile
├── playbook.yml
├── docker-compose.yml
├── grafana
│   ├── Dockerfile
│   ├── node_exporter.yaml
│   └── prometheus.yaml
├── prom
│   ├── Dockerfile
│   └── prometheus.yml
└── readme.md
```


### Vagrantfile

Описание команд для `vagrant up`
- Строки 3-10: задаём образ для VM, кладём файлы, которые понадобятся для дальнейшего запуска
- Строки 14-43: устанавливаем Docker, устанавливаем Docker Compose, настраиваем Docker
- Строки 46-51: устанавливаем и запускаем ansible, используя `playbook.yml`
- Строки 53-56: указываем сколько цпу и памяти выделить VM,  пробрасываем порты для prometeus и grafana

### playbook.yml

Состоит из двух частей: `Install NodeExporter` и `Start Prom&Grafana`.

В `Install NodeExporter` несколько тасок, которые скачивают и разархивируют бинарь NodeExporter, затем создаются службу и запускают её

В `Start Prom&Grafana` с помощью `docker compose` запускаются два контейнера (prometeus и grafana), которые описаны в `docker-compose.yml`

### docker-compose.yml

Файл, который собирает и запускает контейнеры с Графаной и Прометеусом. Использует файлы, лежащие в папках `grafana` и `prom`

#### grafana/
    - Dockerfile - файл для сборки контейнера с Grafana.
    - node_exporter.yaml - файл, настраивающий импорт дашборда NodeExporter в Grafana
    - prometheus.yaml - файл, описывающий настройки для подключения источника данных для Grafana
#### prom/
    - Dockerfile - файл для сборки контейнера с Grafana.
    - prometheus.yml - файл для настройки прометеуса на сбор данных с хостового NodeExporter