# sapor-gate

### Introduction
To solve the problem of oes-SAPOR and spinnaker’s spin-gate communication, the following model is available.

### Summary:
Create another gate called “sapor-gate”, which is essentially a replica of spin-gate
Replace the authentication of this gate with “basic authentication”, allowing us to provide a username and a password.
Basic authentication takes a username and a password. Only the “username” needs to match the admin-user in the real, authenticated spin-gate. Password can be any generic password as only oes-sapor would use it to communicate with the sapor-gate

### Modify the following:
spinnaker.yml:    baseUrl: https://spin.oes37-srini.bogus.net      --- Spinnaker Deck URL
spinnaker.yml:    baseUrl: https://spin-gate.oes37-srini.bogus.net --- Spinnaker gate URL
spinnaker.yml:    baseUrl: redis://:password@oes-redis-master:6379 --- Redis URL

Edit gate-local.yml and edit the username and password, in plain text

### Create sapor-gate config files as a secret
kubectl delete secret sapor-gate-files
kubectl create secret generic sapor-gate-files --from-file gate-local.yml --from-file gate-overrides.yml --from-file gate.yml --from-file sapor-gate-svc.yaml --from-file spinnakerconfig.yml --from-file spinnaker.yml --dry-run -o yaml > basic-auth-secret.yaml

### create secret in same namespace where spinnaker is installed
kubectl apply -f basic-auth-secret.yaml

### create the deployment
kubectl apply -f basic-auth-gate.yaml

### create the service
kubectl apply -f sapor-gate-svc.yaml
