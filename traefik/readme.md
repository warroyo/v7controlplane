helm repo add traefik https://containous.github.io/traefik-helm-chart
kubectl create ns traefik
helm install traefik traefik/traefik --values traefik/values.yml --namespace traefik