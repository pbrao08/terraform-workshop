# Day 2 - Advanced Terraform Concepts, Best Practices and Tricks

## Topics Covered
1. tfvars files
2. Workspaces
3. Hands-On: Create a new workspace
4. Data Sources
5. Provisioners
6. Import
7. Hand-On: Import a resource (lazy hack)
8. Meta Arguments
9. Hand-On: Meta Arguments
10. Expressions (TODO)
11. Functions (TODO)

### 1. tfvars files
- Terraform allows you to define variables in a separate file with the `.tfvars` extension.
- You can define variables in a `.tfvars` file using the `variable_name = value` syntax.
- You can pass the `.tfvars` file to the `terraform apply` command using the `-var-file` flag.

- Resources:
    - https://developer.hashicorp.com/terraform/language/values/variables#variable-definitions-tfvars-files

### 2. Workspaces
- Workspaces allow you to manage multiple states of the same configuration.
- By default, Terraform uses a single workspace called `default`.
- You can create new workspaces using the `terraform workspace new <workspace-name>` command.
- You can switch between workspaces using the `terraform workspace select <workspace-name>` command.
- You can list all workspaces using the `terraform workspace list` command.
- You can delete a workspace using the `terraform workspace delete <workspace-name>` command.

- Resources:
    - https://developer.hashicorp.com/terraform/cli/workspaces

### 3. Hands-On: Create a new workspace

- Create a directory called `workspace-example`.
- Copy the `main.tf` file from the `dev` or `prod` directory to the `workspace-example` directory.
- Change the `backend.tf` file to use a different state file for the new workspace example.
- Create a `dev.tfvars` file with the required variables.
- Create a `prod.tfvars` file with the required variables.
- Run the following commands to create the dev workspace:
    ```bash
    terraform init
    terraform workspace new dev
    terraform apply -var-file=dev.tfvars
    ```
- Review the state file for the dev workspace.
- Run the following commands to create the prod workspace:
    ```bash
    terraform workspace new prod
    terraform apply -var-file=prod.tfvars
    ```
- Review the state file for the prod workspace.

### 4. Data Sources
- Data sources allow you to fetch information from external sources.
- You can use data sources to fetch information from APIs, databases, etc.
- You can use the `data` block to define a data source.
- You can use the `data` block with the `data_type.data_source_name` syntax to fetch data.
- You can use the `data` block with the `data_type.data_source_name.id` syntax to fetch a specific attribute from the data source.

- Resources:
    - https://developer.hashicorp.com/terraform/language/data-sources

```hcl
data "google_compute_network" "network" {
    name = "my-network"
}

resource "google_compute_instance" "instance" {
    name         = "my-instance"
    machine_type = "n1-standard-1"
    zone         = "us-central1-a"
    network      = data.google_compute_network.network.self_link
}
```

### 5. Provisioners
- Provisioners allow you to run scripts on the local machine or on the remote machine.
- Last resort for configuration management.
- You can use the `local-exec` provisioner to run scripts on the local machine. (Mostly used)
- You can use the `remote-exec` provisioner to run scripts on the remote machine.
-- Resources:
    - https://developer.hashicorp.com/terraform/language/resources/provisioners/syntax

### 6. Import
- The `terraform import` command allows you to import existing resources into your Terraform state.
- You can use the `terraform import` command with the resource type and resource ID to import a resource.

```hcl
terraform import google_compute_instance.instance my-instance
```

- Resources:
    - https://developer.hashicorp.com/terraform/cli/commands/import

### 7. Hand-On: Import a resource (lazy hack)
- Create a new directory called `import-example`.
- Create a Cloud Run service using the Google Cloud Console. Add as many configurations as you want.
- Create an empty google_cloud_run_service resource in your Terraform configuration. Set the name to the name of the Cloud Run service.
- Run the terraform import command to import the Cloud Run service into your Terraform state.
```hcl
terraform import google_cloud_run_service.service_name service_name
```
- List the resources using the terraform state list command. `terraform state list`
- Copy the terraform state resource name and run the terraform show command to view the resource details. `terraform show google_cloud_run_service.service_name`
- Copy the resource details to your Terraform configuration file.
- Run the terraform plan command to view the changes.

### 8. Meta Arguments
- Meta arguments allow you to control the behavior of resources.
- You can use the `depends_on` meta argument to define dependencies between resources.
```hcl
resource "google_compute_network" "network" {
    name = "my-network"
}

resource "google_compute_instance" "instance" {
    name         = "my-instance"
    machine_type = "n1-standard-1"
    zone         = "us-central1-a"
    depends_on   = [google_compute_network.network]
}
```

- You can use the `count` meta argument to create multiple instances of a resource.
```hcl
resource "google_compute_instance" "instance" {
    count        = 3
    name         = "my-instance-${count.index}"
    machine_type = "n1-standard-1"
    zone         = "us-central1-a"
}
```

- You can use the `for_each` meta argument to create multiple instances of a resource.
```hcl
variable "buckets" {
    type = map(object({
        location = string
        storage_class = string
    })
    default = {
        bucket1 = {
            location = "us-central1"
            storage_class = "STANDARD"
        }
        bucket2 = {
            location = "us-central1"
            storage_class = "REGIONAL"
        }
    }
}

resource "google_storage_bucket" "bucket" {
    for_each = var.buckets
    name     = each.key
    location = each.value.location
    storage_class = each.value.storage_class
}
```

- You can use the `provider` meta argument to define the provider for a resource.
```hcl
resource "google_compute_instance" "instance" {
    provider     = google-beta
    name         = "my-instance"
    machine_type = "n1-standard-1"
    zone         = "us-central1-a"
}
```

- You can use the `lifecycle` meta argument to define lifecycle hooks for a resource.
```hcl
resource "google_compute_instance" "instance" {
    name         = "my-instance"
    machine_type = "n1-standard-1"
    zone         = "us-central1-a"

    lifecycle {
        create_before_destroy = true
    }
}
```

- Resources:
    - https://developer.hashicorp.com/terraform/language/meta-arguments/lifecycle
    - https://developer.hashicorp.com/terraform/language/meta-arguments/depends_on
    - https://developer.hashicorp.com/terraform/language/meta-arguments/count
    - https://developer.hashicorp.com/terraform/language/meta-arguments/for_each
    - https://developer.hashicorp.com/terraform/language/meta-arguments/resource-provider

### 9. Hand-On: Meta Arguments
- Create a new directory called `meta-arguments-example`.
- Copy the `main.tf` file from the `import-example` directory to the `meta-arguments-example` directory.
- Parameterize the `google_cloud_run_service` resource. Use the `for_each` meta argument to create multiple Cloud Run services.
- Add the `prevent_destroy` lifecycle meta argument to the `google_cloud_run_service` resource to prevent the resource from being destroyed.

### 10. Expressions (TODO)

### 11. Functions (TODO)
