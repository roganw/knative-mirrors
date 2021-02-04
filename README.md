# knative-mirrors

1、下载部署文件并替换gcr.io镜像地址：
curl -Lo ./serving-crds.yaml https://github.com/knative/serving/releases/download/v0.20.0/serving-crds.yaml

curl -Lo ./serving-core.yaml https://github.com/knative/serving/releases/download/v0.20.0/serving-core.yaml && \
sed -i "s/gcr.io.*queue.*/registry.cn-shanghai.aliyuncs.com\/trail\/knative:queue-0.20.0/g" serving-core.yaml && \
sed -i "s/gcr.io.*activator.*/registry.cn-shanghai.aliyuncs.com\/trail\/knative:activator-0.20.0/g" serving-core.yaml && \
sed -i "s/gcr.io.*autoscaler.*/registry.cn-shanghai.aliyuncs.com\/trail\/knative:autoscaler-0.20.0/g" serving-core.yaml && \
sed -i "s/gcr.io.*controller.*/registry.cn-shanghai.aliyuncs.com\/trail\/knative:controller-0.20.0/g" serving-core.yaml && \
sed -i "s/gcr.io.*webhook.*/registry.cn-shanghai.aliyuncs.com\/trail\/knative:webhook-0.20.0/g" serving-core.yaml

curl -Lo ./kourier.yaml https://github.com/knative/net-kourier/releases/download/v0.20.0/kourier.yaml &&  \
sed -i "s/gcr.io.*kourier.*/registry.cn-shanghai.aliyuncs.com\/trail\/knative-kourier:kourier-0.20.0/g" kourier.yaml

curl -Lo ./serving-default-domain.yaml https://github.com/knative/serving/releases/download/v0.20.0/serving-default-domain.yaml && \
sed -i "s/gcr.io.*default-domain.*/registry.cn-shanghai.aliyuncs.com\/trail\/knative-domain:default-domain-0.20.0/g" serving-default-domain.yaml


Mac系统需要再sed -i命令后添加"", 如：
sed -i "" "s/xx/yy/g" serving-core.yaml


2、安装
kubectl apply --filename serving-crds.yaml && \
kubectl apply --filename serving-core.yaml

使用Kourier作为网络层:
kubectl apply --filename kourier.yaml   && \
kubectl patch configmap/config-network --namespace knative-serving --type merge --patch '{"data":{"ingress.class":"kourier.ingress.networking.knative.dev"}}'   && \
kubectl --namespace kourier-system get service kourier

配置域名 默认使用xip.io
kubectl apply --filename serving-default-domain.yaml

使用Istio作为网络层：
kubectl apply --filename https://github.com/knative/net-istio/releases/download/v0.20.0/release.yaml && \
kubectl --namespace istio-system get service istio-ingressgateway

2、查看运行状态，都是running说明正常
kubectl get pods --namespace knative-serving


3、运行服务文件
kubectl apply --filename xxx

