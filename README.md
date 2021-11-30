# SSP EKS Workloads

Welcome to the SSP EKS Workloads repository.

This repository serves as a sample GitOps configuration repository that is meant to be used with the [Amazon EKS SSP Quick Start](https://github.com/aws-quickstart/quickstart-ssp-amazon-eks).

## Documentation  

Please refer to the Amazon EKS SSP Quick Start [Getting Started](https://aws-quickstart.github.io/ssp-amazon-eks/getting-started/) guide for details on how to bootstrap an EKS cluster with the workload configuration contained in this repository.

## Usage 

To bootstrap an EKS cluster with the configuration that is contained in this repository, create a new application in ArgoCD via the following.

```
argocd app create dev-apps \
    --dest-namespace argocd  \
    --dest-server https://kubernetes.default.svc  \
    --repo https://github.com/aws-samples/ssp-eks-workloads.git \
    --path "envs/dev"
```

To sync app configuration and deploy the workloads, run the following.

```
argocd app sync dev-apps 
```

## Repo Structure 

The configuration in this repository is organized into two directories: `envs` and `teams`. 

```
├── envs
└── teams
```

### Environments

The configuration in the `envs` subdirectories represent configuration for a specific EKS environment. In the context of the Amazon EKS SSP Quick Start, an environment maps directly to a single EKS Cluster. 

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
│   ├── dev
│   │   ├── deployment.yaml
│   │   └── service.yaml
│   ├── prod
│   │   ├── deployment.yaml
│   │   └── service.yaml
│   └── test
│       ├── deployment.yaml
│       └── service.yaml
├── team-riker

```
## Security

See [CONTRIBUTING](CONTRIBUTING.md#security-issue-notifications) for more information.

## License

This library is licensed under the MIT-0 License. See the LICENSE file.

