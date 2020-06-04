helm repo add concourse https://concourse-charts.storage.googleapis.com/

helm install concourse concourse/concourse --values concourse/values.yml --namespace ops