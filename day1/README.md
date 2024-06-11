# Day 1 - Introduction to Terraform

## Topics Covered
1. What is Terraform?
2. Infrastructure as Code (IaC) Principles
3. Overview of the Terraform Ecosystem
4. Installing and Configuring Terraform
5. Basic CLI Commands
6. Hands-On: Simple Example of Terraform Script
7. Variables, outputs and Resources
8. Hands-On: Add variables, locals and outputs to the Terraform script.
9. Modules
10. Hands-On: Create a module
11. Hands-On: Use GCP modules
12. Terraform state
13. Hands-On: Terraform state

### 1. What is Terraform?
- Definition: Terraform is an open-source tool for building, changing, and versioning infrastructure safely and efficiently.
- Key Features:
    - Platform-agnostic: Works with multiple cloud providers like AWS, Azure, GCP, and many more.
    - Declarative Configuration Language: Allows you to describe your infrastructure as code.
    - Infrastructure as Code (IaC): Enables versioning, automation, and consistent infrastructure deployment.
- Resources:
    - https://developer.hashicorp.com/terraform/intro

### 2. Infrastructure as Code (IaC) Principles
- Concept: Treat infrastructure the same way you treat application code.
- Benefits:
    - Consistency: Ensures environments are consistent and reproducible.
    - Version Control: Infrastructure changes are versioned and auditable.
    - Automation: Automates the provisioning and management of infrastructure.
    - Declarative Syntax: Define what you want, not how to achieve it.
    - Scalability: Manage infrastructure for projects of any size.
    - Modularity: Reuse configuration for different environments (development, testing, production).
    - Community and Ecosystem: Access a wide range of community modules and provider plugins.
    - State Management: Keep track of your infrastructure's state over time, enabling complex deployments.
- Resources:
    - https://developer.hashicorp.com/terraform/intro/vs
    - https://developer.hashicorp.com/terraform/intro/use-cases


### 3. Overview of the Terraform Ecosystem

- Terraform CLI: The main command-line tool used to manage Terraform configurations.
- Terraform Cloud and Terraform Enterprise: Offer collaborative features, remote state management, and more.
- Providers: Plugins that interact with various APIs (e.g., AWS, Azure, GCP, Kubernetes).
- Modules: Self-contained packages of Terraform configurations that are managed as a group.
- Community Resources:
    - Terraform Registry: A central repository of pre-built modules and providers.
    - HashiCorp Learn: Official tutorials and learning resources.
    - GitHub: Community-contributed modules and examples.
- Resources:
    - https://registry.terraform.io/
    - https://developer.hashicorp.com/terraform/tutorials
    - https://registry.terraform.io/browse/modules
    - https://registry.terraform.io/browse/providers
    - https://developer.hashicorp.com/terraform/cli/commands
    - https://developer.hashicorp.com/terraform/enterprise
    - https://github.com/terraform-google-modules

### 4. Installing and Configuring Terraform

- Install terraform CLI: https://developer.hashicorp.com/terraform/install
- Install tfenv (optional, for installing several terraform versions): https://github.com/tfutils/tfenv
- Install gcloud SDK: https://cloud.google.com/sdk/docs/install
- Install terraform plugin for visual studio: https://marketplace.visualstudio.com/items?itemName=HashiCorp.terraform

- Configuring GCP Provider:
    - Ensure you have the Google Cloud SDK installed and authenticated:
        - `gcloud init`
        - `gcloud auth application-default login`

### 5. Basic CLI Commands

- terraform init: Initializes a working directory containing Terraform configuration files.
- terraform plan: Creates an execution plan, showing what actions Terraform will take.
- terraform apply: Executes the actions proposed in a Terraform plan to reach the desired state.
- terraform destroy: Destroys the infrastructure managed by Terraform.

- Resources:
   - https://developer.hashicorp.com/terraform/intro/core-workflow
   - https://developer.hashicorp.com/terraform/cli/commands/init
   - https://developer.hashicorp.com/terraform/cli/run
   - Format your terraform code: https://developer.hashicorp.com/terraform/cli/commands/fmt

### 6. Hands-On: Simple Example of Terraform Script
- Create a new directory for your project.
- Write a Terraform configuration file (main.tf) with the following content:
```hcl
provider "google" {
  project     = "my-project-id"
  region      = "us-central1"
}

resource "google_storage_bucket" "bucket-example" {
  name          = "bkt-public-bucket"
  location      = "US"
  force_destroy = true

  public_access_prevention = "enforced"
}
```

- Initialize the directory with `terraform init`.
- Plan the changes with `terraform plan`.
- Apply the configuration with `terraform apply`.
- Review the changes and confirm with `yes`.

### 7. Variables, outputs and Resources

- Variables: Input parameters for your Terraform configuration. 
    ```hcl
    variable "bucket_name" {
        description = "Name of the storage bucket"
        type        = string
        default     = "bkt-public-bucket"
    }

    variable "env" {
        description = "Environment for the resources"
        type        = string
        default     = "dev" 
    }
    ```

- Outputs: Values that are exposed to the user after applying the configuration.
    ```hcl
    output "bucket_url" {
        value = google_storage_bucket.bucket-example.url
    }
    ```

- locals: Local values that can be reused within the configuration.
    ```hcl
    locals {
        bucket_name = "${var.bucket_name}-${var.env}"
    }
    #reference to local:
    bucket_name = local.bucket_name
    ```

- Data Types: Strings, numbers, booleans, lists, maps, objects, etc.

- Resource: A block of configuration that defines a single piece of infrastructure.
    ```hcl
    resource "google_storage_bucket" "bucket_example" {
        name          = local.bucket_name
        location      = "US"
        force_destroy = true

        public_access_prevention = "enforced"
    }
    ```

- Resources:
    - https://developer.hashicorp.com/terraform/language/values/variables
    - https://developer.hashicorp.com/terraform/language/values/outputs
    - https://developer.hashicorp.com/terraform/language/values/locals
    - https://developer.hashicorp.com/terraform/language/expressions/types
    - https://developer.hashicorp.com/terraform/language/expressions/type-constraints

### 8. Hands-On: Add variables, locals and outputs to the Terraform script.

- Create a file called `variables.tf` and define the variables for the bucket name and environment.
- Use locals to generate a dynamic bucket name based on the environment.
- Create a file called `outputs.tf` and define an output to display the bucket URL. Define an output to display the bucket URL after applying the configuration.
- Give the bucket IAM roles to users or service account. Use the `google_storage_bucket_iam_member` resource to grant the roles. https://registry.terraform.io/providers/hashicorp/google/latest/docs/resources/storage_bucket_iam#google_storage_bucket_iam_member
- Apply the configuration with `terraform apply`, verify the output and verify the bucket in the GCP console.
- Destroy the infrastructure with `terraform destroy`.

### 9. Modules

- Modules: Reusable packages of Terraform configurations that can be shared and reused across projects.
    - Structure: Consists of a directory with one or more `.tf` files containing Terraform configuration.
    - Inputs: Variables that can be passed to the module.
    - Outputs: Values that can be returned from the module.
    - Resources: Infrastructure components defined within the module.
    - Example:
        ```hcl
        module "storage_bucket" {
            source = "./modules/storage_bucket"

            bucket_name = var.bucket_name
            env         = var.env
        }
        ```

- Resources:
    - https://developer.hashicorp.com/terraform/language/modules
    - https://developer.hashicorp.com/terraform/tutorials/modules/module
    - https://github.com/terraform-google-modules


### 10. Hands-On: Create a module
- Create a new directory called `modules` and add a subdirectory called `storage_bucket`.
- Move the storage bucket configuration from `main.tf`, `variables.tf` and `outputs.tf` to `modules/storage_bucket/main.tf`.
- Create `dev` and `prod` directories in your root directory and create a `main.tf` file in each directory that uses the `storage_bucket` module.
- Apply the configuration for the `dev` environment and verify the output.
- Apply the configuration for the `prod` environment and verify the output.

### 11. Hands-On: Use GCP modules
- Create a new directory called `common` and add a `main.tf` file.
- Use the `terraform-google-modules` to create a GCP storage bucket. https://github.com/terraform-google-modules/terraform-google-cloud-storage
- Name the bucket `bkt-tf-state` and set variable `randomize_suffix` to `true`.
- Set variable `versioning` to: 
```hcl
versioning = {
    bkt-tf-state = true
}
```
- Apply the configuration and verify the bucket in the GCP console.

### 12. Terraform state
- Terraform state: A JSON file that keeps track of the resources managed by Terraform. Check the generated `terraform.tfstate` file in your working directory.
- Remote State: Store the state file in a remote backend (e.g., Terraform Cloud, S3, GCS).
    - Example GCS backend configuration:
        ```hcl
        terraform {
            backend "gcs" {
                bucket  = "my-tf-state-bucket"
                prefix  = "terraform/state"
            }
        }
        ```

- Locking: Prevents concurrent modifications to the state file. Which means only one person can apply the changes at a time.
- State Management: Import existing infrastructure into Terraform state.
- Resources:
    - https://developer.hashicorp.com/terraform/cli/state
    - https://learn.hashicorp.com/tutorials/terraform/state
    - https://developer.hashicorp.com/terraform/language/state

### 13. Hands-On: Terraform state
- Go to directories `dev` and `prod` and create a `backend.tf` file.
- Add the GCS backend configuration to the `backend.tf` file. Change prefix to `dev` and `prod` respectively.
- Initialize the backend with `terraform init` on both directories.
- Reply to the prompt with `yes` to copy the state file to the GCS bucket.
- Apply the configuration and verify the state file in the GCS bucket.

### Extra resources
- Best practices: https://cloud.google.com/docs/terraform/best-practices-for-terraform
- GCP end to end example architectures or blueprints build on terraform:  https://cloud.google.com/docs/terraform/blueprints/terraform-blueprints
- GCP Terraform examples: https://cloud.google.com/docs/terraform/samples