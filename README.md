# terraform-eks-cluster-deployment

This is an automation of EKS and resource creation in AWS. 
I've used terraform to init the EKS cluster.

In order to launch the cluster following commands are useful.

## Commands
### Init & Plan the infra change
This will basicaly plan the infrastructure change that the Terraform is going to apply by using 'apply' command.

```
terraform init -input=false
terraform plan 
```

### Apply
 This will actually execute the 'Apply' and create the EKS cluster. It will do all the resource creation that mentioned in previous step.
 ```
 terraform apply
 ```
 
### Save terraform output to Kubeconfig
You need to save the output of the EKS cluster after terraform creates it.
```
mkdir ~/.kube
terraform output -raw kubeconfig>~/.kube/config
```

Note: After you create the cluster you need to install `kubectl`. This config file will be used to connect to the cluster.
<install kubectl>

### Kube deployment of services, pods, pvc and ingress
You may need to create ingress controller first using `helm`
<install `helm` and IAM authenticator>
```
curl -L https://github.com/kubernetes-sigs/aws-iam-authenticator/releases/download/v0.3.0/heptio-authenticator-aws_0.3.0_linux_amd64 -o /usr/local/bin/aws-iam-authenticator && chmod +x /usr/local/bin/aws-iam-authenticator
curl https://get.helm.sh/helm-v3.5.2-linux-amd64.tar.gz | tar xvz && mv linux-amd64/helm /usr/bin/helm && chmod +x /usr/bin/helm
```

Install ingress controller
```
helm repo add nginx-stable https://helm.nginx.com/stable
helm repo update
helm install my-release nginx-stable/nginx-ingress
```

Create the depoyments, services, pods, and ingress using kubectl ( all the YAML available in `kubemanifests` dir will be applied.
```
kubectl apply -f ./kubemanifests
```


Delete the running services, pods, pvc, ingress
```
kubectl delete -f ./kubemanifests
```



### Destroy
This will destroy the infrastructure in one go. It will actualy plan the destruction of the resources and on confirmation it will actually remove the resource that has been created using 'apply'.

#### Plan destroy
This will showcase the list of resources that terraform can destroy on `destroy` command next.
```
terraform plan -destroy -out tfdestroy
```

Destroy the infrastructure EKS. It will delete the cluster and remove the resources it created earlier.
```
terraform destroy
```
