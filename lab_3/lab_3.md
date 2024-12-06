University: [ITMO University](https://itmo.ru/ru/)

Faculty: [FICT](https://fict.itmo.ru)

Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)

Year: 2024/2025

Group: K4111c

Author: Alekseev Pavel Alekseevich

Lab: Lab3

Date of create: 07.12.2024

Date of finished: 12.12.2024

  

# Лабораторная работа №3 "Сертификаты и "секреты" в Minikube, безопасное хранение данных."

  

## Описание

В данной лабораторной работе вы познакомитесь с сертификатами и "секретами" в Minikube, правилами безопасного хранения данных в Minikube.

  

## Цель работы:

Познакомиться с сертификатами и "секретами" в Minikube, правилами безопасного хранения данных в Minikube.

  

## Ход работы:

В процессе выполнения лабораторной работы были выпонены следующие шаги:

### 1. Был создан манифест configMap

  

```
apiVersion: v1

kind: ConfigMap

metadata:

name: config

data:

REACT_APP_USERNAME: "Pasha"

REACT_APP_COMPANY_NAME: "ITMO"

```


### 2. Был создан манифест deployment
```
apiVersion: apps/v1

kind: Deployment

metadata:

name: app

labels:

app: app

spec:

replicas: 2

selector:

matchLabels:

app: app

template:

metadata:

labels:

app: app

spec:

containers:

- name: app

image: ifilyaninitmo/itdt-contained-frontend:master

ports:

- containerPort: 3000

name: http

envFrom:

- configMapRef:

name: config

```


### 3.  Создал Ingress.

```
apiVersion: networking.k8s.io/v1

kind: Ingress

metadata:

name: app

spec:

tls:

- hosts:

- pashka-lab3.com

secretName: app-tls

rules:

- host: pashka-lab3.com

http:

paths:

- path: /

pathType: Prefix

backend:

service:

name: app

port:

name: http

```

- tls указывает на используемый сертификат, добавленный в minikube.

### 4. Настройки minikube.

  

 `minikube addons enable ingress` был включен ingress.

  

![1](/screens/1.png)

  

Была принята конфигурация из манифеста `kubectl apply -f manifest.yaml`

  

![2](/screens/2.png)

  

Был создан сертификат `openssl req -new -newkey rsa:4096 -x509 -sha256 -days 365 -nodes -out itdt.crt -keyout itdt.key`

  

![3](/screens/3.png)

  

Cертификат был добавлен в minikube `kubectl create secret tls app-tls --key="itdt.key" --cert="itdt.crt"`. Конфигурация манифеста была принята еще раз `kubectl apply -f manifest.yaml`


![4](/screens/4.png)

  

В файле /etc/hosts был добавлен pashka-lab3.com

  ![5](/screens/5.png)

  

## 5. Подключение по FQDN

  

 `minikube tunnel` для доступа к ingress.

  

![6](/screens/6.png)

  

В браузере заходим по указанному доменному имени 

  
![7](/screens/7.png)

  

Cертификат


![8](/screens/8.png)

  

## Схема

  

![9](/screens/9.png)