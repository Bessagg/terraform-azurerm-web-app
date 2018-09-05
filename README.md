# terraform-azurerm-Web-app

## Create a Web App in Azure

This terraform module deploys a Web App on dedicated app service plan, with autoscaling, in Azure. This configuration expects a connection gateway in front and uses ip restriction to only accept incoming traffic from the ip address specified in the variable "gateway_ip".

The following resources will be created by the module:
- App service plan (S1)
- Web app
- Auto scale settings for app service plan. 


## Usage

```hcl

resource "azurerm_resource_group" "image_resizer" {
  name     = "image-resizer-func-rg"
  location = "westeurope"
}

module "Web_app" {
  source                    = "innovationnorway/Web-app/azurerm"
  web_app_name              = "image-resizer-func"
  resource_group_name       = "${azurerm_resource_group.image_resizer.name}"
  location                  = "${azurerm_resource_group.image_resizer.location}"
  environment               = "lab"
  webapp_version            = "beta"
  release                   = "release 2018-07-21.001"
  account_replication_type  = "LRS"
  gateway_ip                = "1.2.3.4"
  
  app_settings {
  }

  tags {
      a       = "b",
      project = "image-resizing"
  }
}

```

## Inputs

### resource_group_name
The resource group where the resources should be created.

### location
The azure datacenter location where the resources should be created. Defaults to "westeurope"

### web_app_name
The name for the Web app. Without environment naming.

### gateway_ip
The ip address to the gateway protecting the web application.

### account_replication_type
The Storage Account replication type. See azurerm_storage_account module for posible values.
Defaults to "LRS"

### app_settings
Application settings to insert on creating the Web app. Following updates will be ignored, and has to be set manually. Updates done on application deploy or in portal will not affect terraform state file.
 
### tags
A map of tags to add to all resources. Release and Environment will be auto tagged. 

### environment
The environment where the infrastructure is deployed.

### release
The release the deploy is based on



## Outputs

### identity
The MSI identities set on the web app. Returns a list of identities.

### identity_tenant_id
The MSI identity tenant id set on the web app.