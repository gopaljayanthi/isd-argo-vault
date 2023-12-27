correct path in put commands.txt

put document in prerequites

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

3. choose the following passwords

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

replace VAULT_MOUNT, VAULT_PATH, VAULT_AUTH

kubectl -n NAMESPACE apply -f s.yaml

7. verify the secrets are created
8. delete all pods 
( make changes to posgres sts ( envs from secret, image, startup script )
 and rabbitmq deployments ( envs from secret) if necessary )
9. make sure all new pods come up ok


