# k8s-nginx-clientes-mongodb

Para rodar a aplicação:

> kubectl create -f https://tonanuvem.github.io/k8s-clientes-nginx-mongodb/cliente-svc-deploy.yaml

Os seguintes serviços serão criados:

```
> ~/k8s-nginx-clientes-mongodb$ kubectl get svc
```

|NAME          |TYPE       |CLUSTER-IP     |EXTERNAL-IP  |PORT(S)         |AGE|
| --- | --- | --- | ---| --- | ---|
|backend        |ClusterIP    |10.106.72.158   |<none>        |5000/TCP   |2m40s
|frontend       |NodePort    |10.99.142.117   |<none>        |80:32000/TCP     |2m40s
|mongo          |ClusterIP   |10.102.58.27    |<none>        |27017/TCP        |2m40s
|mongoexpress   |NodePort    |10.100.3.61     |<none>        |8081:32081/TCP   |2m40s
 
Código fonte da aplicação: https://github.com/tonanuvem/nginx-clientes-microservice-mongodb

<hr>

<li> Testar com Helm:

<ol> Portworks

Versao 1.19.2 ( verificar com kubectl version --short | awk -Fv '/Server Version: / {print $3}' )
 
> kubectl apply -f 'https://install.portworx.com/2.6?mc=false&kbver=1.19.2&oem=esse&user=075ebe88-f8e2-11ea-a2c5-c24e499c7467&b=true&c=px-cluster-3ae228df-0ebe-4a69-bbf5-4c6bdc30cc18&stork=true&lh=true&st=k8s'

> kubectl get pods -o wide -n kube-system -l name=portworx

Demora uns 10 min:

> kubectl -n kube-system describe pods <portworx-pod-id>
 
Events:
   |Type     |Reason                             |Age                     |From                  |Message
   |----     |------                             |----                    |----                  |-------
   |Normal   |Scheduled                          |7m57s                   |default-scheduler     |Successfully assigned kube-system/portworx-qxtw4 to k8s-node-2
   |...
   |Warning  |Unhealthy                          |5m15s (x15 over 7m35s)  |kubelet, k8s-node-2   |Readiness probe failed: HTTP probe failed with statuscode: 503
   |Normal   |NodeStartSuccess                   |5m7s                    |portworx, k8s-node-2  |PX is ready on this node

<ol> Helm
 > helm install --name mongodb --set auth.enabled=false,service.portName=mongo,persistence.existingClaim=px-mongo-pvc bitnami/mongodb
 
 link: https://hub.helm.sh/charts/bitnami/mongodb
