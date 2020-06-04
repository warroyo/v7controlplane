helm repo add hashicorp https://helm.releases.hashicorp.com
kubectl create ns ops
helm install consul hashicorp/consul --values consul/values.yml --namespace ops