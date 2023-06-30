

# flask_app

# Введение
Этот репозиторий содержит Web-приложение, позволяющее получить зачёт по дисциплине контейнеризация и оркестрация приложений 2022/2023.

# Архитектура
Web-приложение реализовано на языке Python с использованием фреймворка [Flask](https://flask.palletsprojects.com/en/2.3.x/).

Docker-контейнер приложения опубликован в Dockerhub: [stepangavrilov/flask_app](https://hub.docker.com/repository/docker/stepangavrilov/flask_app/general).

Для автоматизации процессов CI/CD в проекте используются github actions, включающие в себя: проверку кода, сборку образа и выгрузку его на Dockerhub: [stepangavrilov/flask_app](https://hub.docker.com/repository/docker/stepangavrilov/flask_app/general) при каждом новом коммите.

Приложение развёрнуто в k8s кластере с использованием deployment.
Для k8s кластера настроена система мониторинга Prometheus + Grafana.

# Быстрое начао работы
## Docker
Для установки приложения воспользуйтесь образом с Dockerhub:
```
docker pull stepangavrilov/flask_app:tagname:main

docker run -p -d 5000:5000 stepangavrilov/flask_app:tagname:main
```
Для проверки установки воспользуйтесь командой
```
docker ps
```

![run_image.jpg](images/run_image.jpg 'run_image.jpg')

Как итог работы приложения на порту 5000 вы увидите сообщение, которое поможет вам получить зачёт.

```
localhost:5000
```
![run_image.jpg](images/localhost.jpg 'run_image.jpg')


# Мониторинг работы приложения с применением Minikube K8s cluster
Для установки приложения в кластер K8s [устанавливаем minikube](https://minikube.sigs.k8s.io/docs/start/). 

Клонируем репозиторий на машину
```
git clone https://github.com/GavrilovStepan/2022-2023-application-containerization-and-orchestration-Gavrilov_Stepan_and_Lisica_Nikita.git

```
Запускаем minikube
```
minikube start
```
Создаём deployment и service
```
kubectl apply -f 2022-2023-application-containerization-and-orchestration-Gavrilov_Stepan_and_Lisica_Nikita/infrastructure/deployment.yaml

kubectl apply -f 2022-2023-application-containerization-and-orchestration-Gavrilov_Stepan_and_Lisica_Nikita/infrastructure/service.yaml
```
Для доступа к приложению из внешней сети можно воспользоваться port-forward: 
```
kubectl port-forward --address=0.0.0.0 sum-service 5000
```
В таком случае приложение будет доступно по ip-адресу хоста на порту 5000.
Или создать ingress
```
minikube addons enable ingress

kubectl apply -f 2022-2023-application-containerization-and-orchestration-Gavrilov_Stepan_and_Lisica_Nikita/infrastructure/ingress.yaml
```
Чтобы использовать ingress требуется указать доменное имя в файле ingress.yaml, строка host
```
nano ingress.yaml
```
Применение правил ingress для сервиса service
```
kubectl apply -f 2022-2023-application-containerization-and-orchestration-Gavrilov_Stepan_and_Lisica_Nikita/infrastructure/pro-gra-aler-ingress.yaml
```
После приложение будет доступно по адресу http://YOUR_HOST_NAME/app

# Мониторинг
В качестве мониторинга предлагается использовать решение [kube-prometheus](https://github.com/prometheus-operator/kube-prometheus/). Оно содержит prometheus в качестве источника данных, grafana для визуализации метрик, alertmanager для алертов.

Установка:
```
minikube delete && minikube start --kubernetes-version=v1.26.0 --bootstrapper=kubeadm --extra-config=kubelet.authentication-token-webhook=true --extra-config=kubelet.authorization-mode=Webhook --extra-config=scheduler.bind-address=0.0.0.0 --extra-config=controller-manager.bind-address=0.0.0.0
minikube addons disable metrics-server
git clone https://github.com/prometheus-operator/kube-prometheus
kubectl apply --server-side -f kube-prometheus/manifests/setup
kubectl wait \
	--for condition=Established \
	--all CustomResourceDefinition \
	--namespace=monitoring
kubectl apply -f kube-prometheus/manifests/
```
Для доступа к мониторингу можно воспользоватся port-forward (запуск в разных терминалах)
```
kubectl port-forward --address=0.0.0.0 grafana 3000
kubectl port-forward --address=0.0.0.0 prometheus-k8s 9090
kubectl port-forward --address=0.0.0.0 alertmanager-main 9093
```
Соответственно, доступ к мониторингу по ip-адресу машины. Grafana порт 3000, Prometheus порт 9090, Alertmanager порт 9093.

# Как использовать?
После быстрого старта контейнера вам будет доступен полный функционал приложения.
Далее вы можете дополнять и изменять его возможности в соответствии [API Flask](https://flask.palletsprojects.com/en/2.3.x/).

# Авторы
* Лисица Никита 
* Гаврилов Степан