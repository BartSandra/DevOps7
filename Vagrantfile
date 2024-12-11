Vagrant.configure("2") do |config|
  # Определение настроек VM...
  # Задаём базовый образ VM, который будет использоваться.
  config.vm.box = "ubuntu/focal64"

  # Устанавливливаем VirtualBox в качестве провайдера виртуальных машин
  config.vm.provider "virtualbox" do |vb|
    # Опциональные настройки провайдера
    vb.memory = "1024" # Выделяем 1024 МБ оперативной памяти для VM
    vb.cpus = 2 # Настраиваем VM на использование 2 процессорных ядер
  end

  # Создаем директорию на виртуальной машине
  config.vm.provision "shell", inline: <<-SHELL
    mkdir -p /home/vagrant/services
  SHELL

  # Синхронизация исходного кода между хост-системой и виртуальной машиной
  config.vm.synced_folder "C:/Users/Sandra/DevOps_7-1/src/services", "/home/vagrant/services"
end