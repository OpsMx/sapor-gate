# sapor-gate

### Introduction
To solve the problem of oes-SAPOR and spinnaker’s spin-gate communication, the following model is being proposed.

### Solution Summary:
1. Create another gate called “sapor-gate”, which is essentially a replica of spin-gate
2. Replace the authentication of this gate with “basic authentication”, allowing us to provide a username and a password.
3. Basic authentication takes a username and a password. Only the “username” needs to match the admin-user in the real, authenticated spin-gate. Password can be any generic password as only oes-sapor would use it to communicate with the sapor-gate

All these steps can be done by cloning this repo and following the steps below.

### Update Sapor image if using release 3.7 or earlier
- quay.io/opsmxpublic/ubi8-oes-sapor:v3.6.2.1
- kubectl set image deployment/oes-sapor oes-sapor=quay.io/opsmxpublic/ubi8-oes-sapor:v3.6.2.1

### Modify the following to point to the correct url:
- spinnaker.yml:    baseUrl: redis://:password@oes-redis-master:6379 --- Redis URL

Edit gate-local.yml and edit the **username and password**, in plain text

### Create sapor-gate config files as a secret
- kubectl delete secret sapor-gate-files               IF THIS EXISTS
- kubectl create secret generic sapor-gate-files --from-file gate-local.yml --from-file gate-overrides.yml --from-file gate.yml --from-file sapor-gate-svc.yaml --from-file spinnakerconfig.yml --from-file spinnaker.yml --dry-run -o yaml > basic-auth-secret.yaml

### Create the secret, deployment and service
- kubectl apply -f basic-auth-secret.yaml
- kubectl apply -f basic-auth-gate.yaml
- kubectl apply -f sapor-gate-svc.yaml

### Get the base64 encoded string
- echo -ne "**username:password**" | base64 -w0                  REPLACE username:password as appropriate

### Go to OES-UI
- use http://sapor-gate:8084 as the spinnaker url
- Select LDAP, and enter password as the base64 encoded string above

Once saved, please wait for two minutes and click on Setup->Application->Sync Spinnaker Applications (top right)
