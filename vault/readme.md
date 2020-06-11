helm repo add hashicorp https://helm.releases.hashicorp.com
helm install vault hashicorp/vault --values vault/values.yml --namespace ops

kubectl exec vault-0 -- vault operator init -key-shares=5 -key-threshold=3 -format=json > cluster-keys.json

VAULT_UNSEAL_KEY_1=$(cat cluster-keys.json | jq -r ".unseal_keys_b64[0]") && \
VAULT_UNSEAL_KEY_2=$(cat cluster-keys.json | jq -r ".unseal_keys_b64[1]") && \
VAULT_UNSEAL_KEY_3=$(cat cluster-keys.json | jq -r ".unseal_keys_b64[2]")


kubectl exec vault-0 -- vault operator unseal $VAULT_UNSEAL_KEY_1 && \
kubectl exec vault-0 -- vault operator unseal $VAULT_UNSEAL_KEY_2 && \
kubectl exec vault-0 -- vault operator unseal $VAULT_UNSEAL_KEY_3

kubectl exec vault-1 -- vault operator unseal $VAULT_UNSEAL_KEY_1 && \
kubectl exec vault-1 -- vault operator unseal $VAULT_UNSEAL_KEY_2 && \
kubectl exec vault-1 -- vault operator unseal $VAULT_UNSEAL_KEY_3

kubectl exec vault-2 -- vault operator unseal $VAULT_UNSEAL_KEY_1 && \
kubectl exec vault-2 -- vault operator unseal $VAULT_UNSEAL_KEY_2 && \
kubectl exec vault-2 -- vault operator unseal $VAULT_UNSEAL_KEY_3

export VAULT_ADDR=http://vault.apps.haas-436.pez.pivotal.io
export VAULT_TOKEN=
vault secrets enable -version=1 -path=concourse kv
vault policy write concourse ./vault/concourse.hcl
vault auth enable approle
vault write auth/approle/role/concourse policies=concourse period=1h
vault read auth/approle/role/concourse/role-id
vault write -f auth/approle/role/concourse/secret-id