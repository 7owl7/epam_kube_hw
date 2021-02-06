## Список подов в namespace kube-system:
```shell
oleg@owl-nb:~$ kubectl get pods -n kube-system
NAME                                        READY   STATUS      RESTARTS   AGE
coredns-74ff55c5b-qk8j5                     1/1     Running     10         22d
etcd-minikube                               1/1     Running     10         23h
ingress-nginx-admission-create-ft64h        0/1     Completed   0          4d21h
ingress-nginx-admission-patch-kxw54         0/1     Completed   0          4d21h
ingress-nginx-controller-558664778f-scvfj   1/1     Running     9          4d21h
kube-apiserver-minikube                     1/1     Running     10         22d
kube-controller-manager-minikube            1/1     Running     10         22d
kube-proxy-zlvk8                            1/1     Running     10         22d
kube-scheduler-minikube                     1/1     Running     10         22d
metrics-server-6d74fbf577-6qsjp             1/1     Running     1          23h
storage-provisioner                         1/1     Running     2          23h
```


 1.  Поды с постфиксом -minikube (**etcd-minikube, kube-apiserver-minikube, kube-controller-manager-minikube, kube-scheduler-minikube**) запускаются при старте демона kubelet на узле. Это так называемые Static Pods. Расположение конфигурационных файлов данных подов задано значением staticPodPath в /var/lib/kubelet/config.yaml (в нашем случае это /etc/kubernetes/manifests).
 ```shell
  # ls -l /etc/kubernetes/manifests
 total 16
 -rw------- 1 root root 2289 Feb  2 16:54 etcd.yaml
 -rw------- 1 root root 3595 Feb  2 16:54 kube-apiserver.yaml
 -rw------- 1 root root 2895 Feb  2 16:54 kube-controller-manager.yaml
 -rw------- 1 root root 1385 Feb  2 16:54 kube-scheduler.yaml
 ```

 2. **storage-provisioner** - addon minikube, спецификация пода находится в файле
 /etc/kubernetes/addons/storage-provisioner.yaml. Под стартует при запуске
 minikube, при этом автоматически не перезапускается в случае смерти пода.
 Не смог понять кто именно запускает данный под, при перезапуске сервиса kubelet, при "убитом" поде - новый экземпляр не запускается.


  Поды созданные в рамках deployments запускаются kubelet-ом, который получает информацию о необходимости запуска новых подов (контейнеров) периодически обращаясь к apiserver-у и сверяя полученную информацию с реальным состоянием  ноды на которой он запущен. Назначение пода конкретному узлу осуществляет Kubernetes scheduler.

 3. **ingress-nginx-admission-create-ft64h, ingress-nginx-admission-patch-kxw54** -
  данные поды созданы job-контроллером, это поды для одноразового запуска, которые после успешного завершения своей работы больше не перезапускаются.

 4. **coredns-74ff55c5b-qk8j5**  - создан в рамках Deployment coredns, Controlled By:  ReplicaSet/coredns-74ff55c5b
 5. **ingress-nginx-controller-558664778f** - создан в рамках  Deployment/ingress-nginx-controller  Controlled By:  ReplicaSet/ingress-nginx-controller-558664778f
 6. **metrics-server-6d74fbf577** создан в рамках Deployment metrics-server Controlled By:  ReplicaSet/metrics-server-6d74fbf577
 7. **kube-proxy-zlvk8** - создается контроллером DaemonSet, который запускает экземпляр данного пода на всех узлах кластера

За перезапуск подов непосредственно на узле отвечает kubelet, основываясь на restartPolicy (по умолчанию Always) конкретного пода.
