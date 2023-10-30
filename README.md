# Terraform manifest for Azure Storage Account

```t
terraform {
  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "2.93.0"
    }
    azuread = {
      source = "hashicorp/azuread"
    }
  }
}

provider "azurerm" {
  features {}
  skip_provider_registration = true
}
resource "azurerm_storage_account" "lab" {
  name                     = "[Insert unique storage account name here]"
  resource_group_name      = "[Insert your Resource Group's name here]"
  location                 = "East US"
  account_tier             = "Standard"
  account_replication_type = "LRS"

  tags = {
    environment = "Terraform Storage"
    CreatedBy   = "Admin"
  }
}
```

# Terraform manifest for Azure Storage Account, Container, Blob & Share

```t
terraform {
  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "2.93.0"
    }
    azuread = {
      source = "hashicorp/azuread"
    }
  }
}

provider "azurerm" {
  features {}
  skip_provider_registration = true
}

resource "azurerm_storage_account" "lab" {
  name                     = "newfileandblob4lab"
  resource_group_name      = "Enter Resource Group Name"
  location                 = "East US"
  account_tier             = "Standard"
  account_replication_type = "LRS"
}

resource "azurerm_storage_container" "lab" {
  name                  = "blobcontainer4lab"
  storage_account_name  = azurerm_storage_account.lab.name
  container_access_type = "private"
}

resource "azurerm_storage_blob" "lab" {
  name                   = "TerraformBlob"
  storage_account_name   = azurerm_storage_account.lab.name
  storage_container_name = azurerm_storage_container.lab.name
  type                   = "Block"
}
resource "azurerm_storage_share" "lab" {
  name                 = "terraformshare"  
  storage_account_name = azurerm_storage_account.lab.name
  quota                = 50
}
```

# Terraform manifest for Azure Virtual Network

```t
terraform {
  required_providers {
    azurerm = {
      source = "hashicorp/azurerm"
      version = "= 2.99"
    }
  }
}

provider "azurerm" {
  features {}

  skip_provider_registration = true
}

resource "azurerm_virtual_network" "TFNet" {
    name                = "LabVnet"
    address_space       = ["10.0.0.0/16"]
    location            = "eastus"
    resource_group_name = "Enter Resource Group Name"

    tags = {
        environment = "Terraform Networking"
    }
}

resource "azurerm_subnet" "tfsubnet" {
    name                 = "LabSubnet"
    resource_group_name = "Enter Resource Group Name"
    virtual_network_name = azurerm_virtual_network.TFNet.name
    address_prefixes       = ["10.0.1.0/24"]
}
resource "azurerm_subnet" "tfsubnet2" {
    name                 = "LabSubnet2"
    resource_group_name = "Enter Resource Group Name"
    virtual_network_name = azurerm_virtual_network.TFNet.name
    address_prefixes       = ["10.0.2.0/24"]
}
```

# Terraform manifest for Azure Network Security Group

```t
terraform {
  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "=3.0.0"
    }
  }
}

provider "azurerm" {
  features {}

  skip_provider_registration = true
}

resource "azurerm_network_security_group" "nsg" {
  name                = "LabNSG"
  location            = "East US"
  resource_group_name = "Enter resource group name"
}

resource "azurerm_network_security_rule" "example1" {
  name                        = "Web80"
  priority                    = 1001
  direction                   = "Inbound"
  access                      = "Allow"
  protocol                    = "Tcp"
  source_port_range           = "80"
  destination_port_range      = "80"
  source_address_prefix       = "*"
  destination_address_prefix  = "*"
  resource_group_name         = "Enter Resource group name"
  network_security_group_name = azurerm_network_security_group.nsg.name
}

resource "azurerm_network_security_rule" "example2" {
  name                        = "Web8080"
  priority                    = 1000
  direction                   = "Inbound"
  access                      = "Deny"
  protocol                    = "Tcp"
  source_port_range           = "8080"
  destination_port_range      = "8080"
  source_address_prefix       = "*"
  destination_address_prefix  = "*"
  resource_group_name         = "Enter Resource group name"
  network_security_group_name = azurerm_network_security_group.nsg.name
}

  resource "azurerm_network_security_rule" "example4" {
  name                        = "SSH"
  priority                    = 1100
  direction                   = "Inbound"
  access                      = "Allow"
  protocol                    = "Tcp"
  source_port_range           = "*"
  destination_port_range      = "22"
  source_address_prefix       = "*"
  destination_address_prefix  = "*"
  resource_group_name         = "Enter Resource group name"
  network_security_group_name = azurerm_network_security_group.nsg.name
}

  resource "azurerm_network_security_rule" "example3" {
  name                        = "Web80Out"
  priority                    = 1000
  direction                   = "Outbound"
  access                      = "Deny"
  protocol                    = "Tcp"
  source_port_range           = "80"
  destination_port_range      = "80"
  source_address_prefix       = "*"
  destination_address_prefix  = "*"
  resource_group_name         = "Enter Resource group name"
  network_security_group_name = azurerm_network_security_group.nsg.name
}
```
# Terraform manifest for Azure Virtual Machine

```t
terraform {

  required_version = ">=0.12"

  required_providers {
    azurerm = {
      source = "hashicorp/azurerm"
      version = "~>2.0"
    }
  }
}

provider "azurerm" {
  skip_provider_registration = "true"
  features {}
}

resource "azurerm_virtual_network" "TFNet" {
    name                = "TFVnet"
    address_space       = ["10.0.0.0/16"]
    location            = "East US"
    resource_group_name = "<RESOURCE_GROUP_NAME>"

    tags = {
        environment = "Terraform VNET"
    }
}

resource "azurerm_subnet" "tfsubnet" {
    name                 = "default"
    resource_group_name = "<RESOURCE_GROUP_NAME>"
    virtual_network_name = azurerm_virtual_network.TFNet.name
    address_prefixes       = ["10.0.1.0/24"]
}

resource "azurerm_public_ip" "example" {
  name                = "pubip1"
  location            = "East US"
  resource_group_name = "<RESOURCE_GROUP_NAME>"
  allocation_method   = "Dynamic"
  sku                 = "Basic"
}

resource "azurerm_network_interface" "example" {
  name                = "robot-nic"
  location            = "East US"
  resource_group_name = "<RESOURCE_GROUP_NAME>"

    ip_configuration {
    name                          = "ipconfig1"
    subnet_id                     = azurerm_subnet.tfsubnet.id
    private_ip_address_allocation  = "Dynamic"
    public_ip_address_id          = azurerm_public_ip.example.id
  }
}

resource "azurerm_storage_account" "sa" {
  name                     = "robodiags4tflab"
  resource_group_name      = "<RESOURCE_GROUP_NAME>"
  location                 = "East US"
   account_tier            = "Standard"
   account_replication_type = "LRS"

   tags = {
    environment = "Boot Diagnostic Storage"
    CreatedBy = "Admin"
   }
  }

resource "azurerm_virtual_machine" "example" {
  name                  = "robot"
  location              = "East US"
  resource_group_name   = "<RESOURCE_GROUP_NAME>"
  network_interface_ids = [azurerm_network_interface.example.id]
  vm_size               = "Standard_B1s"
  delete_os_disk_on_termination = true
  delete_data_disks_on_termination = true

  storage_image_reference {
    publisher = "Canonical"
    offer     = "UbuntuServer"
    sku       = "16.04-LTS"
    version   = "latest"
  }

  storage_os_disk {
    name              = "osdisk1"
    disk_size_gb      = "128"
    caching           = "ReadWrite"
    create_option     = "FromImage"
    managed_disk_type = "Standard_LRS"
  }

  os_profile {
    computer_name  = "robot"
    admin_username = "vmadmin"
    admin_password = "Password12345!"
  }

  os_profile_linux_config {
    disable_password_authentication = false
  }

boot_diagnostics {
        enabled     = "true"
        storage_uri = azurerm_storage_account.sa.primary_blob_endpoint
    }
}
```

# Terraform manifest for Azure Web App

```t
terraform {
  required_providers {
    azurerm = {
      source = "hashicorp/azurerm"
      version = "= 2.99"
    }
  }
}

provider "azurerm" {
  features {}

  skip_provider_registration = true
}

resource "azurerm_app_service_plan" "svcplan" {
  name                = "newweb-appserviceplan"
  location            = "eastus"
  resource_group_name = "<YOUR_RESOURCE_GROUP_NAME>"

  sku {
    tier = "Standard"
    size = "S1"
  }
}

resource "azurerm_app_service" "appsvc" {
  name                = "custom-tf-webapp-for-thestudent-[add some unique characters]"
  location            = "eastus"
  resource_group_name = "<YOUR_RESOURCE_GROUP_NAME>"
  app_service_plan_id = azurerm_app_service_plan.svcplan.id


  site_config {
    dotnet_framework_version = "v4.0"
    scm_type                 = "LocalGit"
  }
}
```
# Terraform manifest for Azure MYSQL Database

```t
terraform {
  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "=3.0.0"
    }
  }
}

provider "azurerm" {
  features {}
  skip_provider_registration = true
}

resource "azurerm_mysql_server" "example" {
  name                = "tflab-mysqlserver-1-[some unique characters]"
  location            = "The location of your lab provided resource group"
  resource_group_name = "Enter Your Lab's Resource Group Name"

  sku_name = "B_Gen5_2"
  # capacity = "2"
  # tier = "Basic"
  # family = "Gen4"
        
  storage_mb = "5120"
  version = "8.0"
  backup_retention_days = "7"
  ssl_enforcement_enabled = true
  infrastructure_encryption_enabled = false
  auto_grow_enabled = true
  public_network_access_enabled = true 

  administrator_login          = "mysqladminun"
  administrator_login_password = "easytologin4once!"
}

resource "azurerm_mysql_database" "example" {
  name                = "exampledb"
  resource_group_name = "Enter Your Lab's Resource Group Name"
  server_name         = azurerm_mysql_server.example.name
  charset             = "utf8"
  collation           = "utf8_unicode_ci"
}
```
