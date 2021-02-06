
####  Для развертывания приложения необходимо выполнить следующие команды:
```shell
kubectl create namespace homework
kubectl apply -f nginx-configmap.yaml -f app-prod.yaml -f app-demo.yaml -n homework
kubectl apply -f ingress.yaml -n homework
```
#### Вариант №1 (canary-by-header):
```shell
kubectl apply -f ingress-canary-header.yaml -n homework
curl -H "Host: canary.homework.com" http://INGRESS_EXTERNAL_IP - вызывает основное приложение (app-prod)
curl -H "Host: canary.homework.com" -H "canary:always" http://INGRESS_EXTERNAL_IP - вызывает canary  приложение (app-demo)
```

#### Вариант №2 (canary-weight):
```shell
kubectl apply -f ingress-canary-weight.yaml -n homework
# для проверки
for i in {1.100}; do curl -H "Host: canary.homework.com" http://INGRESS_EXTERNAL_IP; done
```

P.S. В файле hw-part-1.md мои мысли (не уверен что верные) относительно 1-го пункта домашней работы.
