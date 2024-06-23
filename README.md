# Домашнее задание к занятию «Управление доступом» `Горбачёв Олег`

 Инструменты / дополнительные материалы, которые пригодятся для выполнения задания
 1. [Описание](https://kubernetes.io/docs/reference/access-authn-authz/rbac/) RBAC.
 2. [Пользователи и авторизация RBAC в Kubernetes](https://habr.com/ru/company/flant/blog/470503/).
 3. [RBAC with Kubernetes in Minikube](https://medium.com/@HoussemDellai/rbac-with-kubernetes-in-minikube-4deed658ea7b).
 
------
### Задание 1. Создайте конфигурацию для подключения пользователя

1. Создайте и подпишите SSL-сертификат для подключения к кластеру.
2. Настройте конфигурационный файл kubectl для подключения.
3. Создайте роли и все необходимые настройки для пользователя.
4. Предусмотрите права пользователя. Пользователь может просматривать логи подов и их конфигурацию (`kubectl logs pod <pod_id>`, `kubectl describe pod <pod_id>`).
5. Предоставьте манифесты и скриншоты и/или вывод необходимых команд.

------

### Выполнение задания 1. Создайте конфигурацию для подключения пользователя

1. Используя OpenSSL создаю файл ключа:

![img_1](IMG/img_1.png)

Создаю запрос на подписание сертификата (CSR):

![img_2](IMG/img_2.png)

Генерирую файл сертификата (CRT). Поскольку я использую Microk8s, я буду использовать ключи кластера по пути `/var/snap/microk8s/current/certs/`:

![img_3](IMG/img_3.png)

2. Настраиваю конфигурационный файл kubectl для подключения.

Создаю пользователя `staff` и настраиваю его на использование созданного выше ключа:

![img_4](IMG/img_4.png)

Создаю новый контекст с именем `staff-context` и подключаю его к пользователю `staff`, созданному ранее:

![img_5](IMG/img_5.png)

Проверю, создался ли контекст:

![img_6](IMG/img_6.png)

Контекст создался.

3. Для выполнения задания создам отдельный Namespace:

![img_7](IMG/img_7.png)

Также потребуется включение встроенного в Microk8s RBAC контроллера:

![img_8](IMG/img_8.png)

Применю манифест создания роли (Role) и манифест привязки роли к Namespace (RoleBinding):

![img_9](IMG/img_9.png)

4. Для проверки прав пользователя переключусь в его контекст:

![img_10](IMG/img_10.png)

Разверну Deployment в разрешенном для пользователя Namespace:

![img_11](IMG/img_11.png)

Проверю какие развернуты поды в Namespace с именем `default`:

![img_12](IMG/img_12.png)

Видно, что в Namespace с именем `default` нет доступа, так как он не был указан в манифесте Role.

Но если я проверю поды в Namespace с именем `access-control`, то список подов отобразится, т.к. на него есть разрешения в Role:

![img_13](IMG/img_13.png)

Также проверю логи пода:

![img_14](IMG/img_14.png)

Проверю вывод описания пода:

![img_15](IMG/img_15.png)

Согласно манифесту роли и ее привязке к Namespace, у пользователя `staff` есть доступ подам, их логам и описанию.

5. Ссылка на манифест Deployment - https://github.com/RikLedger/12-Kubernetes-09-hw/blob/main/SRC/deployment.yaml

   Ссылка на манифест Role - https://github.com/RikLedger/12-Kubernetes-09-hw/blob/main/SRC/role.yaml

   Ссылка на манифест RoleBinding - https://github.com/RikLedger/12-Kubernetes-09-hw/blob/main/SRC/rolebinding.yaml
