# «Хранение в K8s. Часть 2»- Абрамов Сергей

### Цель задания

В тестовой среде Kubernetes нужно создать PV и продемострировать запись и хранение файлов.

------

### Чеклист готовности к домашнему заданию

1. Установленное K8s-решение (например, MicroK8S).
2. Установленный локальный kubectl.
3. Редактор YAML-файлов с подключенным GitHub-репозиторием.

------

### Дополнительные материалы для выполнения задания

1. [Инструкция по установке NFS в MicroK8S](https://microk8s.io/docs/nfs). 
2. [Описание Persistent Volumes](https://kubernetes.io/docs/concepts/storage/persistent-volumes/). 
3. [Описание динамического провижининга](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/). 
4. [Описание Multitool](https://github.com/wbitt/Network-MultiTool).

------

### Задание 1

**Что нужно сделать**

Создать Deployment приложения, использующего локальный PV, созданный вручную.

1. Создать Deployment приложения, состоящего из контейнеров busybox и multitool.
2. Создать PV и PVC для подключения папки на локальной ноде, которая будет использована в поде.
3. Продемонстрировать, что multitool может читать файл, в который busybox пишет каждые пять секунд в общей директории. 
4. Удалить Deployment и PVC. Продемонстрировать, что после этого произошло с PV. Пояснить, почему.
5. Продемонстрировать, что файл сохранился на локальном диске ноды. Удалить PV.  Продемонстрировать что произошло с файлом после удаления PV. Пояснить, почему.
5. Предоставить манифесты, а также скриншоты или вывод необходимых команд.

------

### Решение


Создадим новый namespace для задания:

![k1](https://github.com/smabramov/K8s-1-7/blob/612bbe89c17a0ec605a3b2e8de07c049076a1863/png/k1.png)

[deployment_b_m.yaml](https://github.com/smabramov/K8s-1-7/blob/612bbe89c17a0ec605a3b2e8de07c049076a1863/code/deployment_b_m.yaml)

![k2](https://github.com/smabramov/K8s-1-7/blob/612bbe89c17a0ec605a3b2e8de07c049076a1863/png/k2.png)

[pv_vol.yaml](https://github.com/smabramov/K8s-1-7/blob/612bbe89c17a0ec605a3b2e8de07c049076a1863/code/pv_vol.yaml)

[pvc_vol.yaml](https://github.com/smabramov/K8s-1-7/blob/612bbe89c17a0ec605a3b2e8de07c049076a1863/code/pvc_vol.yaml)

![k3](https://github.com/smabramov/K8s-1-7/blob/612bbe89c17a0ec605a3b2e8de07c049076a1863/png/k3.png)

Заходим в контейнер multitool и проверяем файл date.log:

![k4](https://github.com/smabramov/K8s-1-7/blob/612bbe89c17a0ec605a3b2e8de07c049076a1863/png/k4.png)

Проверяем файл на ноде:

![k5](https://github.com/smabramov/K8s-1-7/blob/612bbe89c17a0ec605a3b2e8de07c049076a1863/png/k5.png)

Удаляем deployment:

![k6](https://github.com/smabramov/K8s-1-7/blob/612bbe89c17a0ec605a3b2e8de07c049076a1863/png/k6.png)

Проверяем файлы на ноде:

![k7](https://github.com/smabramov/K8s-1-7/blob/612bbe89c17a0ec605a3b2e8de07c049076a1863/png/k7.png)

Файлы остались на ноде. Во-первых, не были удалены pv и pvc, во-вторых, при конфигурировании pv использовался режим ReclaimPolicy: Retain. Retain - после удаления PV ресурсы из внешних провайдеров автоматически не удаляются. После удаления pv файлы так же останутся:

![k8](https://github.com/smabramov/K8s-1-7/blob/612bbe89c17a0ec605a3b2e8de07c049076a1863/png/k8.png)

![k9](https://github.com/smabramov/K8s-1-7/blob/612bbe89c17a0ec605a3b2e8de07c049076a1863/png/k9.png)

### Задание 2

**Что нужно сделать**

Создать Deployment приложения, которое может хранить файлы на NFS с динамическим созданием PV.

1. Включить и настроить NFS-сервер на MicroK8S.
2. Создать Deployment приложения состоящего из multitool, и подключить к нему PV, созданный автоматически на сервере NFS.
3. Продемонстрировать возможность чтения и записи файла изнутри пода. 
4. Предоставить манифесты, а также скриншоты или вывод необходимых команд.

------

### Решение

![k10](https://github.com/smabramov/K8s-1-7/blob/612bbe89c17a0ec605a3b2e8de07c049076a1863/png/k10.png)

[deployment_m.yaml](https://github.com/smabramov/K8s-1-7/blob/612bbe89c17a0ec605a3b2e8de07c049076a1863/code/deployment_m.yaml)

![k11](https://github.com/smabramov/K8s-1-7/blob/612bbe89c17a0ec605a3b2e8de07c049076a1863/png/k11.png)

[sc_nfs.yaml](https://github.com/smabramov/K8s-1-7/blob/612bbe89c17a0ec605a3b2e8de07c049076a1863/code/sc_nfs.yaml)

![k12](https://github.com/smabramov/K8s-1-7/blob/612bbe89c17a0ec605a3b2e8de07c049076a1863/png/k12.png)

[pvc_nfs.yaml](https://github.com/smabramov/K8s-1-7/blob/612bbe89c17a0ec605a3b2e8de07c049076a1863/code/pvc_nfs.yaml)

![k13](https://github.com/smabramov/K8s-1-7/blob/612bbe89c17a0ec605a3b2e8de07c049076a1863/png/k13.png)

Проверим автоматическое создание PV:

![k14](https://github.com/smabramov/K8s-1-7/blob/612bbe89c17a0ec605a3b2e8de07c049076a1863/png/k14.png)

Проверим возможность чтения и записи файла изнутри пода:

![k15](https://github.com/smabramov/K8s-1-7/blob/612bbe89c17a0ec605a3b2e8de07c049076a1863/png/k15.png)

![k16](https://github.com/smabramov/K8s-1-7/blob/612bbe89c17a0ec605a3b2e8de07c049076a1863/png/k16.png)

![k17](https://github.com/smabramov/K8s-1-7/blob/612bbe89c17a0ec605a3b2e8de07c049076a1863/png/k17.png)

### Правила приёма работы

1. Домашняя работа оформляется в своём Git-репозитории в файле README.md. Выполненное задание пришлите ссылкой на .md-файл в вашем репозитории.
2. Файл README.md должен содержать скриншоты вывода необходимых команд `kubectl`, а также скриншоты результатов.
3. Репозиторий должен содержать тексты манифестов или ссылки на них в файле README.md.
