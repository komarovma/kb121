<p class="has-line-data" data-line-start="0" data-line-end="3">Домашнее задание к занятию “12.1 Компоненты Kubernetes”<br>
Используем Windows 10 and Hyper-V<br>
Принтскрины в репозитории</p>
<pre><code>Скачиваем Minikube and kubectl в директорию Q:\Netologia\minikube
New-Item  -Name 'minikube' -ItemType Directory -Force
Invoke-WebRequest -OutFile 'Q:\Netologia\minikube\minikube.exe' -Uri 'https://github.com/kubernetes/minikube/releases/latest/download/minikube-windows-amd64.exe' -UseBasicParsing
Invoke-WebRequest -OutFile 'Q:\Netologia\minikube\kubectl.exe' -Uri 'https://dl.k8s.io/release/v1.23.0/bin/windows/amd64/kubectl.exe' -UseBasicParsing
</code></pre>
<p class="has-line-data" data-line-start="8" data-line-end="10">Powershell от админа<br>
minikube</p>
<pre><code>PS Q:\Netologia\minikube&gt; .\minikube.exe version
minikube version: v1.25.2
commit: 362d5fdc0a3dbee389b3d3f1034e8023e72bd3a7
kubectl.exe
PS Q:\Netologia\minikube&gt; .\kubectl.exe version
Client Version: version.Info{Major:&quot;1&quot;, Minor:&quot;23&quot;, GitVersion:&quot;v1.23.0&quot;, GitCommit:&quot;ab69524f795c42094a6630298ff53f3c3ebab7f4&quot;, GitTreeState:&quot;clean&quot;, BuildDate:&quot;2021-12-07T18:16:20Z&quot;, GoVersion:&quot;go1.17.3&quot;, Compiler:&quot;gc&quot;, Platform:&quot;windows/amd64&quot;}
Server Version: version.Info{Major:&quot;1&quot;, Minor:&quot;23&quot;, GitVersion:&quot;v1.23.3&quot;, GitCommit:&quot;816c97ab8cff8a1c72eccca1026f7820e93e0d25&quot;, GitTreeState:&quot;clean&quot;, BuildDate:&quot;2022-01-25T21:19:12Z&quot;, GoVersion:&quot;go1.17.6&quot;, Compiler:&quot;gc&quot;, Platform:&quot;linux/amd64&quot;}
</code></pre>
<p class="has-line-data" data-line-start="19" data-line-end="20">Настрайваем на работу с Hyper-V</p>
<pre><code>PS Q:\Netologia\minikube&gt; .\minikube.exe start --driver=hyperv --hyperv-virtual-switch=External_LAN
* minikube v1.25.2 на Microsoft Windows 10 Pro 10.0.19044 Build 19044
* Используется драйвер hyperv на основе конфига пользователя
* Downloading VM boot image ...
    &gt; minikube-v1.25.2.iso.sha256: 65 B / 65 B [-------------] 100.00% ? p/s 0s
    &gt; minikube-v1.25.2.iso: 237.06 MiB / 237.06 MiB [ 100.00% 10.47 MiB p/s 23s
* Запускается control plane узел minikube в кластере minikube
* Скачивается Kubernetes v1.23.3 ...
    &gt; preloaded-images-k8s-v17-v1...: 505.68 MiB / 505.68 MiB  100.00% 10.72 Mi
* Creating hyperv VM (CPUs=2, Memory=6000MB, Disk=20000MB) ...
* Подготавливается Kubernetes v1.23.3 на Docker 20.10.12 ...
  - kubelet.housekeeping-interval=5m
  - Generating certificates and keys ...
  - Booting up control plane ...
  - Configuring RBAC rules ...
* Компоненты Kubernetes проверяются ...
  - Используется образ gcr.io/k8s-minikube/storage-provisioner:v5
* Включенные дополнения: storage-provisioner, default-storageclass
* Готово! kubectl настроен для использования кластера &quot;minikube&quot; и &quot;default&quot; пространства имён по умолчанию
</code></pre>
<p class="has-line-data" data-line-start="41" data-line-end="42">Смотрим статус</p>
<pre><code>PS Q:\Netologia\minikube&gt; .\minikube.exe status
minikube
type: Control Plane
host: Running
kubelet: Running
apiserver: Running
kubeconfig: Configured
</code></pre>
<p class="has-line-data" data-line-start="51" data-line-end="52">Создаем дашбоард</p>
<pre><code>PS Q:\Netologia\minikube&gt; kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.5.1/aio/deploy/recommended.yaml
namespace/kubernetes-dashboard created
serviceaccount/kubernetes-dashboard created
service/kubernetes-dashboard created
secret/kubernetes-dashboard-certs created
secret/kubernetes-dashboard-csrf created
secret/kubernetes-dashboard-key-holder created
configmap/kubernetes-dashboard-settings created
role.rbac.authorization.k8s.io/kubernetes-dashboard created
clusterrole.rbac.authorization.k8s.io/kubernetes-dashboard created
rolebinding.rbac.authorization.k8s.io/kubernetes-dashboard created
clusterrolebinding.rbac.authorization.k8s.io/kubernetes-dashboard created
deployment.apps/kubernetes-dashboard created
service/dashboard-metrics-scraper created
deployment.apps/dashboard-metrics-scraper created
</code></pre>
<p class="has-line-data" data-line-start="69" data-line-end="70">Запускаем прокси</p>
<pre><code>PS Q:\Netologia\minikube&gt; kubectl proxy
Starting to serve on 127.0.0.1:8001
</code></pre>
<p class="has-line-data" data-line-start="74" data-line-end="75">В другом окне настрайваем токены доступа</p>
<pre><code>PS C:\WINDOWS\system32&gt; cd Q:\Netologia\minikube\
PS Q:\Netologia\minikube&gt; .\kubectl apply -f .\dashboard-adminuser.yaml
serviceaccount/admin-user created
PS Q:\Netologia\minikube&gt; .\kubectl.exe apply -f .\dashboard-adminuser_cluster_role_binding.yaml
clusterrolebinding.rbac.authorization.k8s.io/admin-user created
PS Q:\Netologia\minikube&gt; .\kubectl.exe -n kubernetes-dashboard describe secret $(.\kubectl.exe -n kubernetes-dashboard get secret | sls admin-user | ForEach-Object { $_ -Split '\s+' } | Select -First 1)
Name:         admin-user-token-8ljtp
Namespace:    kubernetes-dashboard
Labels:       &lt;none&gt;
Annotations:  kubernetes.io/service-account.name: admin-user
              kubernetes.io/service-account.uid: 1a250084-be38-4797-976d-58ff9ae6ad52

Type:  kubernetes.io/service-account-token

Data
====
token:      eyJhbGciOiJSUzI1NiIsImtpZCI6InJ4OHkxOEhQRXNYZmsyaklvcFE2X2ZkcG84NENQaEVWUkljeHhvbWVrNjAifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlcm5ldGVzLWRhc2hib2FyZCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJhZG1pbi11c2VyLXRva2VuLThsanRwIiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQubmFtZSI6ImFkbWluLXVzZXIiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC51aWQiOiIxYTI1MDA4NC1iZTM4LTQ3OTctOTc2ZC01OGZmOWFlNmFkNTIiLCJzdWIiOiJzeXN0ZW06c2VydmljZWFjY291bnQ6a3ViZXJuZXRlcy1kYXNoYm9hcmQ6YWRtaW4tdXNlciJ9.lfHXZQ8xoumFAh_Ggx-Yyy4jzjFs3sLCtM0uctaFIuAecZD0X4aE7UGo3p1HPpbpHsmSxfh8CHcPsAwRatHL3hK2mSt3ZR3mATp3soXhzBj6GKnL99B5bl9xJuVO8h4I2sWUXAmT7QzfWWd739NhFeqC7NS-H_EWpeQCr3bS8Lq4eSZo3x4Q0iHwGJg0eMWmdW7PQpLtbtQ_Jg_5yZ63D1pEHG3kbKjvbvVy-GZpkJMVTTNgFhpw8t1pXG_HJAqhTPcveXulou-rjuOLFzwF3oOXgAmmwmshpfkoabHfSK8ubUpxbswT8s5MXWH55QryRiDgCgF2l-A8DcPP3Ld15A
ca.crt:     1111 bytes
namespace:  20 bytes
</code></pre>
<p class="has-line-data" data-line-start="97" data-line-end="98">Делаем деплой</p>
<pre><code>PS Q:\Netologia\minikube&gt; kubectl create deployment hello-node --image=k8s.gcr.io/echoserver:1.4
deployment.apps/hello-node created
</code></pre>
<p class="has-line-data" data-line-start="101" data-line-end="102">Проверяем деплой</p>
<pre><code>PS Q:\Netologia\minikube&gt; kubectl get deployments
NAME         READY   UP-TO-DATE   AVAILABLE   AGE
hello-node   1/1     1            1           16s
</code></pre>
<p class="has-line-data" data-line-start="107" data-line-end="108">Добавляеем аддон ingress</p>
<pre><code>PS Q:\Netologia\minikube&gt; .\minikube addons enable ingress
  - Используется образ k8s.gcr.io/ingress-nginx/controller:v1.1.1
  - Используется образ k8s.gcr.io/ingress-nginx/kube-webhook-certgen:v1.1.1
  - Используется образ k8s.gcr.io/ingress-nginx/kube-webhook-certgen:v1.1.1
* Verifying ingress addon...
* The 'ingress' addon is enabled
PS Q:\Netologia\minikube&gt; .\kubectl get pod,svc -n kube-system
NAME                                   READY   STATUS    RESTARTS   AGE
pod/coredns-64897985d-wk2nx            1/1     Running   0          16m
pod/etcd-minikube                      1/1     Running   0          16m
pod/kube-apiserver-minikube            1/1     Running   0          16m
pod/kube-controller-manager-minikube   1/1     Running   0          16m
pod/kube-proxy-hzn7q                   1/1     Running   0          16m
pod/kube-scheduler-minikube            1/1     Running   0          16m
pod/storage-provisioner                1/1     Running   0          16m

NAME               TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)                  AGE
service/kube-dns   ClusterIP   10.96.0.10   &lt;none&gt;        53/UDP,53/TCP,9153/TCP   16m
</code></pre>