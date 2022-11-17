Домашняя работа №1 

Цель домашнего задания
Научиться обновлять ядро в ОС Linux. Получение навыков работы с Vagrant, Packer и публикацией готовых образов в Vagrant Cloud. 

Описание домашнего задания
1) Обновить ядро ОС из репозитория ELRepo
2) Создать Vagrant box c помощью Packer
3) Загрузить Vagrant box в Vagrant Cloud

Дополнительные задания:
Ядро собрано из исходников
В образе нормально работают VirtualBox Shared Folders

Предварительная подготовка:
делаем форк https://github.com/dmitry-lyutenko/manual_kernel_update
переносим на локальную машину  git clone  git@github.com:altot/manual_kernel_update.git

Задание 1 Обновить ядро ОС из репозитория ELRepo
	Выполняем команды:
	vagrant up
	vagrant ssh
	sudo yum install -y http://www.elrepo.org/elrepo-release-7.0-3.el7.elrepo.noarch.rpm
	sudo yum --enablerepo elrepo-kernel install kernel-ml -y
	sudo grub2-mkconfig -o /boot/grub2/grub.cfg
	sudo grub2-set-default 0
	sudo reboot
	vagrant ssh
	uname -r - тут убеждаемся, что ядро обновилось.
Задание 2 Создать Vagrant box c помощью Packer
	Используем готовые конфиги из manual_kernel_update
	Убираем неактуальный в текущей версии packer параметр "iso_checksum_type" из centos.json
	Выполняем команды:
	packer build centos.json
Проверяем полученный образ:
	vagrant box add centos7-kernel6 centos-7.7.1908-kernel-5-x86_64-Minimal.box 
 	vagrant box list
Создаем Vagrantfile из полученного box:
	vagrant init centos7-kernel
	vagrant up
	vagrant ssh
Вроде все крутится как задумывалось.

Задание 3 Загрузить Vagrant box в Vagrant Cloud
Авторизуемся в cloud
	vagrant cloud auth login
Публикуем полученный box:
	vagrant cloud publish --release altot/centos7-kernel6 1.0 virtualbox  centos-7.7.1908-kernel-5-x86_64-Minimal.box
Модифицируем Vagrantfile, теперь он используем нужный box из cloud
Проверяем, все работает. Не сделаны доп задания, слишком много времени ушло на борьбу с новой методичкой.
