Vagrant.configure("2") do |config|
  # Указываем образ для VM
  config.vm.box = "generic/ubuntu2004"
  # Переносим внутрь VM файлы для ансибла и докера (playbook.yml, docker_compose.yml).
  # Можно писать их прям внутри ансибла, но так будет неудобно дебажить
  # Побочный эффект - можно будет изменять файлы внутри VM не перезапуская саму VM - vagrant будет синхронизировать их на лету
  config.vm.synced_folder ".", "/home/vagrant/"

  # Устанавливаем Docker и Ansible
  config.vm.provision "shell", inline: <<-SHELL
    # Обновление пакетов и установка необходимых зависимостей
    sudo apt-get update
    sudo apt-get install -y software-properties-common

    # Установка Docker
    sudo apt-get install -y \
        ca-certificates \
        curl \
        gnupg \
        lsb-release
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
    sudo apt-get update
    sudo apt-get install -y docker-ce docker-ce-cli containerd.io
    # Добавляем пользователя, чтобы можно было запускать docker без sudo
    sudo usermod -aG docker $USER

    #  Установка Docker Compose
    sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
    sudo chmod +x /usr/local/bin/docker-compose

    # Прописываем настройки для docker
    # - пул ip-адресов и шлюз для контейнеров
    # - зеркала для скачивания образов, если надо запустить ВМ из России (https://habr.com/ru/news/818177/)
    # И в конце рестартим докер, чтобы считались настрйоки
    sudo echo '{
        "live-restore": true,
        "bip": "10.11.12.1/24",
        "default-address-pools": [{"base": "10.11.12.0/24", "size": 24}],
        "registry-mirrors" : [ "https://huecker.io", "https://mirror.gcr.io", "https://ghcr.io" ]
    }' > /etc/docker/daemon.json && systemctl restart docker

    # Установка Ansible
    sudo apt-add-repository --yes --update ppa:ansible/ansible
    sudo apt-get install -y ansible
    # Установка модуля для запуска docker_cpmpose
    sudo ansible-galaxy collection install community.docker
    # Запуск плейбука
    ansible-playbook /home/vagrant/playbook.yml
  SHELL


  # Указываем ресурсы VM
  config.vm.provider "virtualbox" do |vb|
    vb.memory = "1024"
    vb.cpus = 2

  # Проброс портов между виртуалкой и хостом
  config.vm.network "forwarded_port", guest: 9090, host: 9090  # Prometheus
  config.vm.network "forwarded_port", guest: 3000, host: 3000  # Grafana

  end
end
