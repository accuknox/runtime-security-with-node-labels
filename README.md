# runtime-security-with-node-labels

# Install KubeArmor

This guide shows you how to install Accuknox runtime security solution, on specific nodes in your cluster using a node selector.

This approach gives you granular control over where the agent is deployed, helping you manage resource consumption and target critical nodes.

## 1. Generate KubeArmor config override for node selector

Update the labels variable with the key-value pairs of your target nodes. If you have multiple labels, separate them with a comma. Then run the script.

```bash
#labels="app:nginx,env:production"
labels=""

echo "" > node-config.yaml; {echo -e "kubearmorOperator:\n  nodeSelector:"; echo "$labels" | tr ',' '\n' | while IFS=':' read label value; do echo "    $label: $value"; done} >> node-config.yaml;{echo -e "kubearmorConfig:\n  globalNodeSelector:"; echo "$labels" | tr ',' '\n' | while IFS=':' read label value; do echo "    $label: $value"; done} >> node-config.yaml
```

## 2. Install latest kubearmor-operator using helm chart

Once your configuration file is ready, use the Helm chart to install KubeArmor Operator.

```shell
helm upgrade --install kubearmor-operator \
    --version v1.6.3 oci://public.ecr.aws/k9v9d5v2/runtime-security/kubearmor-operator \
    -n kubearmor --create-namespace --set kubearmorOperator.image.tag=latest \
    --set autoDeploy=true -f node-config.yaml
```