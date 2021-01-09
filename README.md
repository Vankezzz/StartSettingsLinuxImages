# Создание рабочего образа ubuntu
## Задание:
> 1. Добавление пользователя в файл sudoers
> 2. Развернуть сервис из 1го ДЗ на 2х виртуальных машинах в любом облаке. 
> 3. На отдельной виртуальной машине поднять балансировщик и настроить его на проксирование на сервис погоды из ДЗ 1

## Введение
На данный момент верcия операционной системы Ubuntu 18.04 LTS, в которой всего два пользователя user и администратор root с одинаковыми паролями : qwerty
Проверить версию можно при помощи команды : `lsb_release -a `
```
No LSB modules are available.
Distributor ID: Ubuntu
Description:    Ubuntu 18.04 LTS
Release:    18.04
Codename:   bionic 
```
## Добавление пользователя в файл sudoers
1. Заходим под божественным пользователем, который все видит и все слышит root: `sudo -i`
2. Вводим пароль для root: `qwerty`
> Если для root не был установлен пароль, то переходим по этому источнику: https://losst.ru/parol-root-v-ubuntu 
3. Мы хотим разрешить пользователю user запускать команды sudo без запроса пароля, для этого мы откроем файл /etc/sudoers: `visudo`
4. Прокрутим вниз и добавим в самом конце: `user  ALL=(ALL) NOPASSWD:ALL`
> Сохраните файл и закройте редактор. user - это пользователь, которому мы предоставили доступ (замените на любого другого)
5. Добавление пользователя в группу sudo происходит с помощью команды: `usermod -aG sudo user`
6. Выходим из под root с помощью ctrl+D и можем пользоваться (я проверяю это обычно успешным запуском команды sudo apt-get install git)


### Ссылки с более подробным описанием действий
1. https://andreyex.ru/operacionnaya-sistema-linux/kak-dobavit-polzovatelya-sudoers-v-debian/




2. переходим в папку TaskAvito2
3. Создаем образ с нашим сервисом погоды: docker build -t <my_image> -f <my_dockerfile> . : `sudo docker build -t weatherservice -f Dockerfile .`
>* <my_image> - имя нашего контейнера, допустим weatherservice
>* <my_dockerfile> - имя нашего докер файла, в нашем случае его имя Dockerfile
>* Не забудь точку в конце команды!!!
4. Запускаем наш первый контейнер: `sudo docker run -it --rm -p 5000:80 -e OWM_API_KEY=2d6d053b6a777cd0fe236dd2c0d9aa22 -e LOCALHOST_REDIS=redis -e PORT_REDIS=6379 --net=redis --name example1 weatherservice &`
> Он остановится почему то, поэтому мы перезапустим командой : `sudo docker restart example1`
5. Запускаем наш второй контейнер: `sudo docker run -it --rm -p 5001:80 -e OWM_API_KEY=2d6d053b6a777cd0fe236dd2c0d9aa22 -e LOCALHOST_REDIS=redis -e PORT_REDIS=6379 --net=redis --name example2 weatherservice &`
> Он остановится почему то, поэтому мы перезапустим командой : `sudo docker restart example2`


## На отдельной виртуальной машине поднять балансировщик и настроить его на проксирование на сервис погоды из ДЗ 1
1. На третьем сервере необходимо установить Nginx  и заменить конфигурационный файл /etc/nginx/nginx.config нашим: ` sudo mv nginx.conf /etc/nginx/`
2. Потом выполнить команду перезапуска Nginx'а: `sudo nginx -s reload`
3. Проверить статус: `sudo service nginx status`
4. Запустить еще два окна терминала для параллельной прослушки портов наших серверов 5000 и 5001 и ввести канал прослушки: `sudo tcpdump -i any port 5000` и `sudo tcpdump -i any port 5001` в соответственных окнах
5. В основном окне проверить работоспособность сервера отправив запрос `curl http://localhost:80/v1/current/?city=Moscow `
6. Посмотреть в двух вспомогательных окнах как происходят запросы

