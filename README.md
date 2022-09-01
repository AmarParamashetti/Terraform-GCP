# Terraform-GCP  
### Download link Terraform and GCP :  
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
 6. Data source Block : 
 7. Modules Block  
 
 **Terraform Block :** 
 ```
 terraform {
  required_providers {
    google = {
      source  = "hashicorp/aws"
      version = "~> 3.0"
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
    credentails = "<path_to_key.json>"
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
   
 Putting it all together and creating our first instance on google Cloud using Terraform :  
 
 ```
  terraform {
  required_providers {
    google = {
      source  = "hashicorp/aws"
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
  
 resource "google_compute_instance" "default" {
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
 Use of variable and creating instance using variables[create a file <filename.tf>]
 
 ```
   variable "gcp_project_id" {
    default = "<your_project_id>"
  }
   variable "gcp_region" {
    default = "us-central1"
  }
   variable "gcp_zone" {
    default = "us-east1b"
  }
   variable "gcp_machinename" {
    default = "tf-vm-instance"
  }
 ```
 Using the variable in our tf [terraform] script  
 ```
  terraform {
  required_providers {
    google = {
      source  = "hashicorp/aws"
      version = "~> 3.0"
    }
  }
}  

 provider "google" {
    **project = var.gcp_project_id**
    **region  = var.gcp_region**
    **zone = var.gcp_zone**
    credentails = "<path_to_key.json>"
  }  
  
 resource "google_compute_instance" "default" {
  **name         = var.gcp_machinename**
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
 
