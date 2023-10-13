# ARO - ROSA - Declarative

We assume you have a running cluster and credentials for ARO and ROSA.

## Creating the credentials

Run the following to create the secrets needed containing the credentials needed to standup ARO and ROSA clusters

```sh
# Azure Credentials
export client_id=<fill-here-with-yours>
export client_secret=<fill-here-with-yours>
export subscription_id=<fill-here-with-yours>
export tenant_id=<fill-here-with-yours>

# AWS Credentials
export aws_access_key_id=<fill-here-with-yours>
export aws_secret_access_key=<fill-here-with-yours>

#OCM Credentials
export ocm_token=<fill-here-with-yours>

# ROSA Admin credentials
export rosa_username=<fill-here-with-yours>
export rosa_password=<fill-here-with-yours>

oc new-project crossplane-system
oc delete secret azure-secret -n crossplane-system
envsubst < azure-credentials.json > /tmp/azure-credentials.json
oc create secret generic azure-secret -n crossplane-system --from-file=creds=/tmp/azure-credentials.json


oc delete secret aws-secret -n crossplane-system
envsubst < aws-credentials.txt > /tmp/aws-credentials.txt
oc create secret generic aws-secret -n crossplane-system --from-file=creds=/tmp/aws-credentials.txt

oc delete secret ocm-token -n crossplane-system
oc create secret generic ocm-token -n crossplane-system --from-literal=token=${ocm_token}

oc new-project rosa-decl
oc delete secret cluster-admin -n rosa-decl
envsubst < rosa_cluster_admin.json > /tmp/rosa_cluster_admin.json
oc create secret generic cluster-admin -n rosa-decl --from-file=admin-json=/tmp/rosa_cluster_admin.json
```



## Argocd Bootstrap

This will kick off the Day2 configuration in the hub cluster.

```sh
oc apply -f boostrap/subscription.yaml
oc apply -f boostrap/argocd.yaml -n openshift-gitops
oc apply -f boostrap/cluster-rolebinding.yaml -n openshift-gitops
oc apply -f boostrap/applicationset.yaml -n openshift-gitops
```

After the the configurations are deployed, the provisioning of ARO and ROSA clusters will start automatically.