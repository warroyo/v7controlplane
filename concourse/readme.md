helm repo add concourse https://concourse-charts.storage.googleapis.com/

helm install concourse concourse/concourse --values concourse/values.yml --namespace ops

fly -t v7 login -c http://concourse.apps.haas-436.pez.pivotal.io/