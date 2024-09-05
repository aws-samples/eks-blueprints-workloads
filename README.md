# EKS Blueprints Workloads

Welcome to the EKS Blueprints Workloads repository.

This repository serves as a sample GitOps configuration repository that is meant to be used with the [Amazon EKS Blueprints CDK Quick Start](https://github.com/aws-quickstart/cdk-eks-blueprints) and [Amazon EKS Blueprints for Terraform](https://github.com/aws-ia/terraform-aws-eks-blueprints).

## Documentation  

Please refer to the Amazon EKS Blueprints Quick Start [Getting Started](https://aws-quickstart.github.io/cdk-eks-blueprints/getting-started/) guide for details on how to bootstrap an EKS cluster with the workload configuration contained in this repository.

## Usage

### Usage for Multi Repository example

Under the `multi-repo` folder you can find an example of bootstrapping ArgoCD App of Apps application that points to other applications that resides in other git repositories. This way a deployment of every application can be managed independently by the application team in the application's repository. The platform team on the other hand, own this repository (the ArgoCD app of apps configuration) and only need to onboards new applications to the cluster by adding them to the App of Apps chart

To bootstrap an EKS cluster with the configuration under the `multi-repo` folder, you first have to apply the ArgoCD [Projects](https://argo-cd.readthedocs.io/en/stable/user-guide/projects/) manifests resides in the [argo-app-projects](./multi-repo/argo-app-projects/) folder. These Projects are used by the ArgoCD Applicaitons defined in the [argo-app-of-apps](./multi-repo/argo-app-of-apps/) folder. The projects are used in order to restrict the deployment of an application to a specific target namespace. This way we ensure that even if the application team try to deploy their application to a different namespace (by adding explicit namespace definition to their Kubernetes manifests), ArgoCD will not apply the application manifests because of that restriction. Applying the manifests is done by:

```bash
kubectl apply -f multi-repo/argo-app-projects/
```

Now we can onboard the applications into our cluster:

```bash
argocd app create dev-apps \
    --dest-namespace argocd  \
    --dest-server https://kubernetes.default.svc  \
    --repo https://github.com/aws-samples/eks-blueprints-workloads.git \
    --path "multi-repo/argo-app-of-apps/dev"
```

To sync app configuration and deploy the workloads, run the following.

```
argocd app sync dev-apps 
```

### Usage for Single Repository example

To bootstrap an EKS cluster with the configuration that is contained in this repository, create a new application in ArgoCD via the following.

```
argocd app create dev-apps \
    --dest-namespace argocd  \
    --dest-server https://kubernetes.default.svc  \
    --repo https://github.com/aws-samples/eks-blueprints-workloads.git \
    --path "envs/dev"
```

To sync app configuration and deploy the workloads, run the following.

```
argocd app sync dev-apps 
```
### Usage for Securing Ingress using Cognito

`secure-ingress-cognito-*` to be used only when you are using the blueprint pattern named `secure-ingress-blueprint` from the blueprint patterns repository. This workload has been isolated because without Cognito resources this application will fail deploying while using app of apps pattern. 

## Repo Structure

The configuration in this repository is organized into two directories: `envs` and `teams`.

```
├── multirepo
    └── argo-app-of-apps
        └── dev
        └── test
        └── prod
    └── argo-app-projects
├── envs
└── teams
```

### Environments

The configuration in the `envs` subdirectories represent configuration for a specific EKS environment. In the context of the Amazon EKS Blueprints Quick Start, an environment maps directly to a single EKS Cluster.

This repository has support for three different environments: `dev`, `test`, and `prod`. Each environment directory is a Helm chart which references the teams that run workloads in each cluster.

```
envs
├── dev
│   ├── Chart.yaml
│   ├── templates
│   │   ├── team-burnham.yaml
│   │   ├── team-carmen.yaml
│   │   ├── team-riker.yaml
│   └── values.yaml
├── prod
└── test
```

### Teams

The configuration in the `teams` subdirectories represent the individual teams that are running workloads in our EKS clusters.

Each `team` subdirectory in turn has environment subdirectories. The configuration in those directories are Kubernetes manifests that represent the individual workload that is running for each team and in each environment. 

**NOTE** Some team environment directories may contain additional `yaml` files such as an `ingress.yaml`

```
teams
├── team-burnham
│   ├── dev
│   │   ├── Chart.yaml
│   │   ├── templates
│   │   │   ├── deployment.yaml
│   │   │   ├── service.yaml
│   │   └── values.yaml
│   ├── prod
│   │   ├── Chart.yaml
│   │   ├── templates
│   │   │   ├── deployment.yaml
│   │   │   ├── service.yaml
│   │   └── values.yaml
│   ├── test
│   │   ├── Chart.yaml
│   │   ├── templates
│   │   │   ├── deployment.yaml
│   │   │   ├── service.yaml
│   │   └── values.yaml
├── team-spock
│   ├── dev
│   │   ├── Chart.yaml
│   │   ├── templates
│   │   │   ├── common-provider-config-aws.yaml
│   │   │   ├── clusterA-cluster-auth.yaml
│   │   │   ├── clusterA-addon-eks-pod-identity-agent.yaml
│   │   └── values.yaml
├── team-riker

```

The `team-spock` is a platform team which implements the CDK EKS Blueprints pattern [GitOps based Multi-cluster add-on and Apps Management using Crossplane and ArgoCD](https://github.com/aws-samples/cdk-eks-blueprints-patterns/blob/main/docs/patterns/crossplane-argocd-gitops.md)

## Security

See [CONTRIBUTING](CONTRIBUTING.md#security-issue-notifications) for more information.

## License

This library is licensed under the MIT-0 License. See the LICENSE file.
