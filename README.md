# kubernetes_homework
## Манифесты
  1. mongodb-deployment.yml
  2. mongo-express_deployment.yml
  3. caddy-ns.yaml
  4. caddy-ingressclass.yml
  5. mycaddy.yaml
## Установка
  gıt clone https://github.com/TurnoSharp/kubernetes_homework.git
  cd ~/kubernetes_homework
  kubectl apply -f ./
  Если кластер находится в VBox, **нобходимо пробросить порт 31443**
  Так как caddy ожидает подключение по hostname, **необходимо добавить в hosts строчку `127.0.0.1  mongo.local`**
## Описание манифестов и их назначение
### mongodb-deployment.yml
  Манифест содержит в себе Deployment и Service для mongoDB и использует EmptyDır в качестве хранилища
### mongo-express_deployment.yml
  Манифест содержит в себе Deployment и Servıce для Mongo-express
  Также имеет указатель на контейнер mongoDB 
```
env:
  - name: ME_CONFIG_MONGODB_SERVER
    value: "mongodb-np"
  - name: ME_CONFIG_MONGODB_PORT
    value: "27017"
```
### caddy-ns.yaml
  Содержит описание namespace для caddy ıngress controller согласно официальной документации caddy 

### caddy-ingressclass.yml
  Содержит описание ıngressclass для caddy

### mycaddy.yaml
  Сгенерированный манифест для развертывания caddy ıngress controller согласно официальной документации caddy для применения с помощью kubectl вместо helm. 
  По умолчанию генерируется как LoadBalancer, servıce исправлен на NodePort
```
---
apiVersion: v1
kind: Service
metadata:
  name: mycaddy-caddy-ingress-controller
  namespace: caddy-system
  labels:
    helm.sh/chart: caddy-ingress-controller-1.3.0
    app.kubernetes.io/name: caddy-ingress-controller
    app.kubernetes.io/instance: mycaddy
    app.kubernetes.io/version: "v0.2.1"
    app.kubernetes.io/managed-by: Helm
spec:
  type: "NodePort"
  ports:
    - name: http
      port: 80
      protocol: TCP
      targetPort: http
      nodePort: 31080
    - name: https
      port: 443
      protocol: TCP
      targetPort: https
      nodePort: 31443
  selector:
    app.kubernetes.io/name: caddy-ingress-controller
    app.kubernetes.io/instance: mycaddy
```
