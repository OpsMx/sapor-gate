# sapor-gate
spin-gate modified for sapor to communicate using basic-auth

Modify the following:
spinnaker.yml:    baseUrl: https://spin.oes37-srini.bogus.net      --- Spinnaker Deck URL
spinnaker.yml:    baseUrl: https://spin-gate.oes37-srini.bogus.net --- Spinnaker gate URL
spinnaker.yml:    baseUrl: redis://:password@oes-redis-master:6379 --- Redis URL


# Create sapor-gate config files as a secret
kubectl delete secret sapor-gate-files
kubectl create secret generic sapor-gate-files --from-file gate-local.yml --from-file gate-overrides.yml --from-file gate.yml --from-file sapor-gate-svc.yaml --from-file spinnakerconfig.yml --from-file spinnaker.yml --dry-run -o yaml > basic-auth-secret.yaml

# create secret in same namespace where spinnaker is installed
kubectl apply -f basic-auth-secret.yaml

# create the deployment
kubectl apply -f basic-auth-gate.yaml

