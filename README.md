# Kubernetes Dashboard

Kubernetes 是一个通用的、基于web的UI Kubernetes集群。它允许用户管理集群中运行的应用程序和诊断,以及管理集群本身。

## 创建Dashboard TLS证书
```sh
mkdir -p /usr/local/src/kubernetes/certs
cd /usr/local/src/kubernetes
openssl genrsa -des3 -passout pass:x -out certs/dashboard.pass.key 2048
openssl rsa -passin pass:x -in certs/dashboard.pass.key -out certs/dashboard.key
openssl req -new -key certs/dashboard.key -out certs/dashboard.csr -subj '/CN=kube-dashboard'
openssl x509 -req -sha256 -days 365 -in certs/dashboard.csr -signkey certs/dashboard.key -out certs/dashboard.crt
rm certs/dashboard.pass.key
kubectl create secret generic kubernetes-dashboard-certs --from-file=certs -n kube-system
```

## 部署Dashboard服务
```sh
kubectl apply -f https://raw.githubusercontent.com/Donyintao/kubernetes-dashboard/master/kubernetes-dashboard-deployment.yaml
```

## 部署Dashboard Ingress服务
```sh
kubectl apply -f https://raw.githubusercontent.com/Donyintao/kubernetes-dashboard/master/kubernetes-dashboard-ingress.yaml
```

## 身份认证

登陆`dashboard`的时候支持`kubeconfig`和`token`两种认证方式，`kubeconfig`中也依赖`token`字段，所以生成`token`这一步是必不可少的。

### 创建admin用户
```sh
kubectl apply -f https://raw.githubusercontent.com/Donyintao/kubernetes-dashboard/master/kubernetes-dashboard-admin-role.yaml
``` 

### 获取secret和token的值
```sh
# 获取admin-token的secret名字
kubectl get secret -n kube-system|grep admin-token
admin-token-n9xkv                                kubernetes.io/service-account-token   3         5h
# 获取token的值
kubectl describe secret admin-token-n9xkv -n kube-system   
Name:         admin-token-n9xkv
Namespace:    kube-system
Labels:       <none>
Annotations:  kubernetes.io/service-account.name=admin
              kubernetes.io/service-account.uid=c7bb876a-7e6f-11e8-9b5a-5254000ac38c

Type:  kubernetes.io/service-account-token

Data
====
ca.crt:     1359 bytes
namespace:  11 bytes
token:      eyJhbGciOiJSUzI1NiIsImtpZCI6IiJ9.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlLXN5c3RlbSIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJhZG1pbi10b2tlbi1uOXhrdiIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50Lm5hbWUiOiJhZG1pbiIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50LnVpZCI6ImM3YmI4NzZhLTdlNmYtMTFlOC05YjVhLTUyNTQwMDBhYzM4YyIsInN1YiI6InN5c3RlbTpzZXJ2aWNlYWNjb3VudDprdWJlLXN5c3RlbTphZG1pbiJ9.31mVb6sMoHbQrwTpaPhSU3tMCQKnKiVk2RUVBqFpSHzEi0tmWWy5lGJGrd4VWWNl-KW-DdWoxmoFfSwJlrtut5JMzk2XzChHg_Ks7rOFm_hf_1PljiKAdviRYbvFHabVTlDc8mmi8rXAldbqFxnGkg9xqsvnEci-_4HW-NOpljRHpQa7XCqVZujA3a1xdLjG87vqIG384QEcThDmEExyM1PrypHbPrOt5GzWxOR0BvY4Te5MjDCZTVqQDApYKpdmNkZQaN2h7Lgu0JiBEcQsKnAFN7x3JiRybYJJsY8Fi3n1AkQucDXeUKJKUJ1yFq76JVJUYDbevK9P7ckPA3kpVw
```

### 登录校验

可自行测试，登录时选择令牌登录，输入刚才获取到的`token`值即可登录。待补充.....
