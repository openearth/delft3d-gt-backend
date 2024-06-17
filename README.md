[![Build Status](https://travis-ci.org/openearth/delft3d-gt-provisioning.svg?branch=develop)](https://travis-ci.org/openearth/delft3d-gt-provisioning)

# delft3d-gt-backend
Backend for Delft3D GT

# Setup local environment
Requirements
* Ansible version 2.0.0.2 (In virtualenv)
* VirtualBox
* Vagrant
* git
* Nfs server
* Clone of openearth/delft3d-gt-server in ..
* Clone of openearth/delft3d-gt-ui in ..

Provision Vagrant box:
* Make checkout the repository
* browse to the folder with checkout
* Start provisioning by running: vagrant up

# Cloud deployment

This guide provides step-by-step instructions to deploy an AWS EKS (Elastic Kubernetes Service) cluster using the Ansible k8s playbook.

Before you begin, ensure you have the following installed and configured on your system:

1. **Ansible**: Version 2.16.7 (or the last used version)
2. **AWS CLI**: Version 2
3. **WSL2**: Windows Subsystem for Linux 2 (if you are using Windows)

## Setting Up the Ansible Playbook

1. **Review and Edit `all.yml`**:
    - Navigate to the `group_vars` directory and review the `all.yml` file to ensure all input parameters are correctly set.
    ```bash
    cd group_vars
    nano all.yml
    ```
    - Make sure you have the correct values for your environment, such as AWS region, cluster name, and other configurations.

## Deploying the EKS Cluster

1. **Run the Ansible Playbook**:
    - Execute the playbook to create all AWS resources, including the EKS cluster.
    ```bash
    ansible-playbook site_k8s.yml
    ```

### Breakdown of the Playbook

- **Playbook File**: `site_k8s.yml`
- **Roles**:
    - `aws_s3`: Creates necessary S3 buckets for the EKS cluster.
    - `aws_eks`: Provisions the EKS cluster.
    - `aws_efs`: Sets up EFS (Elastic File System) for the cluster.
    - `k8s_argo`: Deploys Argo workflows or related configurations.
    - `k8s_addons`: Deploys additional Kubernetes add-ons.

## Post-Deployment Steps

After successfully deploying the EKS cluster, you may want to:

1. **Verify the Cluster**:
    - Use kubectl to check the status of your cluster.
    ```bash
    kubectl get nodes
    ```

2. **Configure kubectl**:
    - Update your kubeconfig file to interact with the new EKS cluster.
    ```bash
    aws eks --region <your-region> update-kubeconfig --name <your-cluster-name>
    ```

## Troubleshooting

- **Common Issues**:
    - When deploying a new cluster the current deployment workflow will fail while the cluster is creating. After the cluster is done creating you will need to run the deployment again to install Argo and all addons.
    - Workflows will be run in the default namespace. Role bindings need to be added for the Argo namespace if you want to run workflows in that namespace.