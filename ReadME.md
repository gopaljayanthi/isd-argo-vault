1. identify 

VAULT_AUTH_MOUNT the mount used for auhtenticating with kubernetes as defined in operator values.yaml
controller:
  manager:
    clientCache:
      storageEncryption:
        mount: demo-auth-mount

VAULT_MOUNT the mount on which the paths are created
VAULT_ROLE the role that has read rights on VAULT_MOUNT
VAULT_AUTH the vaultauth crd created in namespace
NAMESPACE the namespace where the secrets will be created

2. make sure prerequisites are met from pre-requisites.txt
vault kubernetes authentication enabled
vault auth enable -path VAULT_AUTH_MOUNT kubernetes

vault connect to kubernetes
vault write auth/VAULT_AUTH_MOUNT/config \
   kubernetes_host="https://$KUBERNETES_PORT_443_TCP_ADDR:443"

vault path enabled to store secrets
vault secrets enable -path=VAULT_MOUNT kv-v2


vault create read only policy 
vault policy write dev - <<EOF
path "VAULT_MOUNT/*" {
   capabilities = ["read"]
}

vault create role with read-only policy created above, and associate with namespace in cluster
vault write auth/VAULT_AUTH_MOUNT/role/VAULT_ROLE \
   bound_service_account_names=default \
   bound_service_account_namespaces=NAMESPACE \
   policies=dev \
   audience=vault \
   ttl=24h

4. choose the following passwords

postgrespassword
rabbitmqpassword
redispassword

4. replace in files in secrets folder
sed -i "@POSTGRES-PASSWORD@$postgrespassword" secrets/*
sed -i "@RABBITMQ-PASSWORD@$rabbitmqpassword" secrets/*
sed -i "@REDIS-PASSWORD@$redispassword" secrets/*

5. put secrets into vault using put-commands.txt after replacing 

VAULT_MOUNT
VAULT_PATH
POSTGRES-PASSWORD
RABBITMQ-PASSWORD
REDIS-PASSWORD

6. set values.yaml and run helm commands example helm template . > ss.yaml

kubectl -n NAMESPACE apply -f ss.yaml

7. verify the secrets are created
8. delete all pods 
( make changes to posgres sts ( envs from secret, image, startup script )
 and rabbitmq deployments ( envs from secret) if necessary )
9. make sure all new pods come up ok


