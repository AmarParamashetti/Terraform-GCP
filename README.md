# Terraform-GCP  
### Download link for Terraform and GCP account creation:  
https://www.terraform.io/downloads   
https://cloud.google.com
  
### Terraform Commands :
**terraform init** : it will initilize and add the terraform dependency files to current folder  
**terraform validate** : it will valid and check the syntax if it is correct  
**terraform plan** : it will not create a resource on cloud but it will display what all actions will be taken once we run the apply command  
**terraform apply** : it will create a resources on the cloud, it will ask for confirmation for "Yes" once we run the command [interactive mode]  
**terraform destroy** : it will destroy the resources on the cloud, it will ask for confirmation for "Yes" once we run the command[interactive mode]  
**terraform apply -auto-approve** : it is an alternative for point 6, it will create resource without asking for approval  
**terraform destory -auto-approve** : it is an alternative for point 7, it will create resource without asking for approval  
**terraform fmt** : it will format the terraform syntax
  
 ### Terraform Top level blocks :  
 1. Terraform settings Block :
 2. Provider Block :  
 3. Resource Block :  
 4. Input variable Block : 
 5. Output variable Block :
 6. Local Block :  
 7. Data source Block : 
 8. Modules Block  
 
 **Terraform Block :** 
 ```
 terraform {
  required_providers {
    google = {
      source  = "hashicorp/google" 
      version = "~> 3.0" // provider version
    }
  }
}
 ```  
   
   **Provider Block :**  
 ```
 provider "google" {
    project = "my-project-id"
    region  = "us-central1"
    zone = "us-east1b"
    credentails = "<path_to_key.json>" // key provided in json format from service account
  }
  ```   
  
  **Resource Block :**  
  ```
  resource "<resource_name>" "<local_name>" {
    <arguments> : <value>
}
  ```   
  
  **Input Block :**  
  
  ```
  variable "<variable_name>" {
    default = "<variable_value>"
  }
  ```  
  
  **Output Blocks:**  
  ```
  output "<output_name>" {
    value = <resource_name_and_attribute_to_get_values_from_resource>
}
  ```
  **Datasource Block:** 
  ```
  will update once we use this
  ```   
  
  **Modules Block:**  
    
  ```
  will update once we use this
  ```
   
 Example 1: Putting it all together and creating our first instance on google Cloud using Terraform :  
 
 ```
  terraform {
  required_providers {
    google = {
      source  = "hashicorp/google"
      version = "~> 3.0"
    }
  }
}  

 provider "google" {
    project = "my-project-id"
    region  = "us-central1"
    zone = "us-east1b"
    credentails = "<path_to_key.json>"
  }  
  
 resource "google_compute_instance" "vm-instance" {
  name         = "tf-vm-instance"
  machine_type = "e2-medium"
  zone         = "us-central1-a"

  tags = ["http-server","https-server"] // enable http port

  boot_disk {
    initialize_params {
      image = "debian-cloud/debian-11" //image
    }
  }
  network_interface {
    network = "default"
    access_config {
      // Ephemeral public IP
    }
  }
  metadata_startup_script = <file_path_and_name>
}

 ```   
Example 2:  Use of variable and creating instance using variables[create a file <filename.tf>]
 
 ```
   variable "gcp_project_id" {
    type = string
    default = "<your_project_id>"
  }
   variable "gcp_region" {
    type = string
    default = "us-central1"
  }
   variable "gcp_zone" {
    type = string
    default = "us-east1b"
  }
   variable "gcp_machinename" {
    type = string
    default = "tf-vm-instance"
  }
    variable "gcp_machinetype" {
    type = string
    description ="this variable will be prompted when we run plan or apply command, since we did not provide the default value"
  }  
  
 ```  
 **Note:**   
 1.we can override the default value while running using CLI i.e -var "<variable_name>=<new_value>"  e.g -var "gcp_machinename=tf-instance"  
 2.we can override using environment variables e.g. export TF_VAR_gcp_machinename=tf-export-machinename , here TF_VAR is the environment variable which will replace the default value once we run the plan and apply command we can echo the variable using echo $TF_VAR_gcpmachinename, we can unset the value using command "unset TF_VAR_gcp_machinename"  
 3.we can create a plan file for us and variable will be stored in the file e.g. terraform plan -var "gcp_machinename=tf_instance" -var "gcp_zone=us-east1b" -out <file_name>.plan and we can use the same in apply command e.g terraform apply <file_name>.plan  
 
 Using the variable in our tf [terraform] script  
 ```
  terraform {
  required_providers {
    google = {
      source  = "hashicorp/google"
      version = "~> 3.0" 
    }
  }
}  

 provider "google" {
    project = var.gcp_project_id
    region  = var.gcp_region
   zone = var.gcp_zone
    credentails = "<path_to_key.json>"
  }  
  
 resource "google_compute_instance" "vm-instance" {
  name         = var.gcp_machinename
  machine_type = var.gcp_machinetype
  zone         = "us-central1-a"

  tags = ["http-server","https-server"]

  boot_disk {
    initialize_params {
      image = "debian-cloud/debian-11"
    }
  }
  network_interface {
    network = "default"
    access_config {
      // Ephemeral public IP
    }
  }
  metadata_startup_script = <file_path_and_name>
}
 ```   
 Example 3:  Use of output after creating the instance
  ```
  terraform {
  required_providers {
    google = {
      source  = "hashicorp/google"
      version = "~> 3.0"
    }
  }
}  

 provider "google" {
    project = "my-project-id"
    region  = "us-central1"
    zone = "us-east1b"
    credentails = "<path_to_key.json>"
  }  
  
 resource "google_compute_instance" "vm-instance" {
  name         = "tf-vm-instance"
  machine_type = "e2-medium"
  zone         = "us-central1-a"

  tags = ["http-server","https-server"]

  boot_disk {
    initialize_params {
      image = "debian-cloud/debian-11"
    }
  }
  network_interface {
    network = "default"
    access_config {
      // Ephemeral public IP
    }
  }
  metadata_startup_script = <file_path_and_name>
}
 ```  
 Output : we can use multiple attribute and get the details of resources
 ```
 output "InstanceID" {
  description = "Get the instnace ID of created instance"
  value = google_compute_instance.vm-instance.instance_id  
}  
 output "HostName" {
  description = "Get the instnace ID of created instance"
  value = google_compute_instance.vm-instance.hostname  
}
 ```  
 Example of using local block :  
 ```
 
 ```
 
