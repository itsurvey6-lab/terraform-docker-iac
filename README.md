# Infrastructure as Code with Terraform and Docker

## DevOps Internship — Task 3

### Project Overview

This project demonstrates Infrastructure as Code (IaC) using **Terraform** to provision and manage a Docker container.

Terraform was used to:

* Configure the Docker provider
* Download the Nginx Docker image
* Create an Nginx container
* Expose the container through a host port
* Verify the deployed container
* Track infrastructure using Terraform state
* Destroy the infrastructure using Terraform

## Technologies Used

* Terraform
* Docker
* Nginx
* Ubuntu Linux
* Git and GitHub

## Project Structure

```text
terraform-docker-iac/
├── main.tf
├── README.md
├── .gitignore
└── .terraform.lock.hcl
```

Terraform state files and the `.terraform` working directory are excluded from Git using `.gitignore`.

## Terraform Configuration

The infrastructure is defined in `main.tf`.

The project uses the Docker provider from the Terraform Registry.

The configuration creates:

1. An Nginx Docker image
2. An Nginx Docker container named `terraform-nginx`

The container exposes:

```text
Host Port:      8089
Container Port: 80
```

## Terraform Workflow

### 1. Initialize Terraform

```bash
terraform init
```

Terraform initialized the project and downloaded the required Docker provider.

### 2. Review the Execution Plan

```bash
terraform plan
```

Terraform displayed the resources that would be created.

Expected result:

```text
Plan: 2 to add, 0 to change, 0 to destroy.
```

### 3. Provision the Infrastructure

```bash
terraform apply
```

Terraform created the Docker resources.

Final successful deployment:

```text
Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

The Docker image had already been downloaded during an earlier apply attempt, so the successful apply only needed to create the container.

### 4. Verify the Docker Container

```bash
docker ps
```

The Terraform-managed container was running:

```text
terraform-nginx
```

The port mapping was:

```text
0.0.0.0:8089 -> 80/tcp
```

### 5. Test the Nginx Application

The application was tested using:

```bash
curl http://localhost:8089
```

The response displayed the Nginx welcome page, confirming that the container was running successfully.

## Terraform State

Terraform state was checked using:

```bash
terraform state list
```

The managed resources were:

```text
docker_container.nginx
docker_image.nginx
```

This demonstrated that Terraform was tracking the Docker infrastructure through its state.

## Destroy Infrastructure

After completing the deployment and verification, the infrastructure was removed using:

```bash
terraform destroy
```

Terraform successfully removed both resources:

```text
docker_container.nginx: Destruction complete
docker_image.nginx: Destruction complete

Destroy complete! Resources: 2 destroyed.
```

The Docker environment was then verified:

```bash
docker ps -a
```

The `terraform-nginx` container was no longer present.

Finally:

```bash
terraform state list
```

returned no resources, confirming that the Terraform state was empty.

## Port Conflict Troubleshooting

During the initial deployment, host port `8080` was already being used by a Java process.

The port was checked with:

```bash
sudo ss -ltnp | grep :8080
```

Port `8081` was also found to be in use.

Instead of stopping the existing services, the Terraform configuration was changed to use host port `8089`:

```hcl
ports {
  internal = 80
  external = 8089
}
```

The deployment then completed successfully.

This demonstrated basic troubleshooting of host-port conflicts while avoiding disruption to existing services.

## Key Terraform Concepts

### What is Infrastructure as Code?

Infrastructure as Code is the practice of defining and managing infrastructure using configuration files instead of manually creating infrastructure.

### What is Terraform?

Terraform is an Infrastructure as Code tool used to define, provision, modify, and destroy infrastructure using declarative configuration.

### What is a Provider?

A provider allows Terraform to communicate with a specific platform or service.

In this project, the Docker provider allows Terraform to manage Docker resources.

### What is a Resource?

A resource represents an infrastructure component managed by Terraform.

This project uses:

```text
docker_image
docker_container
```

### What is Terraform State?

Terraform state records Terraform's knowledge of the infrastructure it manages.

The state allows Terraform to compare the desired configuration with the current infrastructure.

### Difference Between `terraform plan` and `terraform apply`

`terraform plan` previews the changes Terraform intends to make.

`terraform apply` actually performs those changes.

### What is `terraform destroy`?

`terraform destroy` removes the infrastructure managed by the Terraform configuration.

## Learning Outcome

This project provided practical experience with:

* Infrastructure as Code
* Terraform configuration
* Terraform providers
* Docker resource provisioning
* Terraform state management
* Infrastructure planning
* Infrastructure deployment
* Infrastructure destruction
* Basic infrastructure troubleshooting

## Conclusion

The project successfully demonstrated how Terraform can be used to provision and manage Docker infrastructure using code.

The complete Infrastructure as Code lifecycle was tested:

```text
Terraform Configuration
        ↓
terraform init
        ↓
terraform plan
        ↓
terraform apply
        ↓
Infrastructure Verification
        ↓
terraform state
        ↓
terraform destroy
        ↓
Infrastructure Removed
```
