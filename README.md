# Репозиторий для выполнения домашних заданий курса "Инфраструктурная платформа на основе Kubernetes-2024-02" 

#### Задание №1

В манифест Deployments добавлен параметр minReadySeconds для возможности наблюдениея за процессом rolling update

```bash
kubectl config set-context --current --namespace homework
kubectl apply -f namespace.yaml -f deloyment.yaml
# Запуск процесса rolling update изменением образа контейнера web-server
kubectl set image deployment/kubernetes-controllers web-server=busybox:1.35.1
# Проверка успешности rolling update
kubectl rollout  status deployment kubernetes-controllers
kubectl get pods
```


#### Задание со *

Назначение метки для ноды:

```bash
# Вывод существующих нод кластера kubernetes с текущими метками
kubectl get nodes --show-labels
# Назначение ноде minikube новой метки node-homework со значением deployment
kubectl label nodes minikube homework=true
# Проверка новой метки
kubectl get nodes -o=jsonpath='{range .items[0]}[node-name:{.metadata.name}, labels:homework={.metadata.labels.homework}]{end}'
```

Добавление конструкции nodeSelector в манифест Deployments 

```yaml
# spec.template.spec
      ...
      nodeSelector:
        node-homework: 'true'
```
Выполнение проверки:

```bash
kubectl label nodes minikube homework=false --overwrite
kubectl apply -f deployment.yaml
kubectl get deployment kubernetes-controllers
#    NAME                     READY   UP-TO-DATE   AVAILABLE   AGE
#    kubernetes-controllers   0/3     3            0           14s
 
kubectl get pods

#    NAME                                      READY   STATUS    RESTARTS   AGE
#    kubernetes-controllers-5cc94666d4-bnwcd   0/1     Pending   0          19s
#    kubernetes-controllers-5cc94666d4-jwlz4   0/1     Pending   0          19s
#    kubernetes-controllers-5cc94666d4-kx92n   0/1     Pending   0          19s

kubectl label nodes minikube homework=true --overwrite
kubectl get deployment kubernetes-controllers
#    NAME                     READY   UP-TO-DATE   AVAILABLE   AGE
#    kubernetes-controllers   0/3     3            0           114s
kubectl get pods
#    NAME                                      READY   STATUS    RESTARTS   AGE
#    kubernetes-controllers-5cc94666d4-bnwcd   0/1     Running   0          118s
#    kubernetes-controllers-5cc94666d4-jwlz4   0/1     Running   0          118s
#    kubernetes-controllers-5cc94666d4-kx92n   0/1     Running   0          118s



```