---
title: 'Tutoriel : Créer un cluster de machines virtuelles Azure avec Terraform et HCL'
description: Utilisez Terraform et HCL pour créer un cluster de machines virtuelles Linux avec un équilibreur de charge dans Azure
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 1ff13f05a5be463ed7477b4bbbc3e1f977a04a75
ms.sourcegitcommit: 2c59a05cb3975bede8134bc23e27db5e1f4eaa45
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2020
ms.locfileid: "75665372"
---
# <a name="tutorial-create-an-azure-vm-cluster-with-terraform-and-hcl"></a>Tutoriel : Créer un cluster de machines virtuelles Azure avec Terraform et HCL

Dans ce tutoriel, vous allez apprendre à créer un petit cluster de calcul à l’aide de [HCL](https://www.terraform.io/docs/configuration/syntax.html). 

Vous allez apprendre à effectuer les tâches suivantes :

> [!div class="checklist"]
> * Configurer l’authentification Azure.
> * Créer un fichier de configuration Terraform.
> * Utiliser un fichier de configuration Terraform pour créer un équilibreur de charge.
> * Utiliser un fichier de configuration Terraform pour déployer deux machines virtuelles Linux dans un groupe à haute disponibilité.
> * Initialisez Terraform.
> * Créer un plan d’exécution Terraform.
> * Appliquer le plan d’exécution Terraform pour créer les ressources Azure.

## <a name="1-set-up-azure-authentication"></a>1. Configurer l’authentification Azure

> [!NOTE]
> Si vous [utilisez des variables d’environnement Terraform](/azure/virtual-machines/linux/terraform-install-configure) ou exécutez ce didacticiel dans [Azure Cloud Shell](terraform-cloud-shell.md), ignorez cette section.

Dans cette section, vous générez un service Azure principal et deux fichiers de configuration Terraform contenant les informations d’identification du principal sécurité.

1. [Configurez un principal de service Azure AD](/azure/virtual-machines/linux/terraform-install-configure#set-up-terraform-access-to-azure) pour permettre à Terraform d’approvisionner des ressources dans Azure. Lors de la création du principal, notez les valeurs pour l’ID d’abonnement, le client, l’appId et le mot de passe.

2. Ouvrez une invite de commandes.

3. Créez un répertoire vide dans lequel stocker vos fichiers Terraform.

4. Créez un nouveau fichier qui contient les déclarations de variables. Vous pouvez nommer ce fichier comme vous le souhaitez avec une extension `.tf`.

5. Copiez le code suivant dans votre fichier de déclarations de variables :

   ```hcl
   variable subscription_id {}
   variable tenant_id {}
   variable client_id {}
   variable client_secret {}
  
   provider "azurerm" {
      subscription_id = var.subscription_id
      tenant_id = var.tenant_id
      client_id = var.client_id
      client_secret = var.client_secret
   }
   ```

6. Créez un nouveau fichier qui contient les valeurs de vos variables Terraform. Il est courant de nommer votre fichier de variables Terraform `terraform.tfvars`, car Terraform charge automatiquement tous les fichiers nommés `terraform.tfvars` (ou suivant un modèle de `*.auto.tfvars`) en cas de présence dans le répertoire actif. 

7. Copiez le code suivant dans votre fichier de variables. Veillez à remplacer les espaces réservés comme suit : Pour `subscription_id`, utilisez l’ID d’abonnement Azure que vous avez spécifié lors de l’exécution de `az account set`. Pour `tenant_id`, utilisez la valeur `tenant` retournée par `az ad sp create-for-rbac`. Pour `client_id`, utilisez la valeur `appId` retournée par `az ad sp create-for-rbac`. Pour `client_secret`, utilisez la valeur `password` retournée par `az ad sp create-for-rbac`.

   ```hcl
   subscription_id = "<azure-subscription-id>"
   tenant_id = "<tenant-returned-from-creating-a-service-principal>"
   client_id = "<appId-returned-from-creating-a-service-principal>"
   client_secret = "<password-returned-from-creating-a-service-principal>"
   ```

## <a name="2-create-a-terraform-configuration-file"></a>2. Créer un fichier de configuration Terraform

Dans cette section, vous créez un fichier qui contient les définitions des ressources pour votre infrastructure.

1. Créez un nouveau fichier appelé `main.tf`. 

2. Copiez l’exemple suivant de ressource dans le fichier `main.tf` nouvellement créé : 

   ```hcl
   resource "azurerm_resource_group" "test" {
    name     = "acctestrg"
    location = "West US 2"
   }

   resource "azurerm_virtual_network" "test" {
    name                = "acctvn"
    address_space       = ["10.0.0.0/16"]
    location            = azurerm_resource_group.test.location
    resource_group_name = azurerm_resource_group.test.name
   }

   resource "azurerm_subnet" "test" {
    name                 = "acctsub"
    resource_group_name  = azurerm_resource_group.test.name
    virtual_network_name = azurerm_virtual_network.test.name
    address_prefix       = "10.0.2.0/24"
   }

   resource "azurerm_public_ip" "test" {
    name                         = "publicIPForLB"
    location                     = azurerm_resource_group.test.location
    resource_group_name          = azurerm_resource_group.test.name
    allocation_method            = "Static"
   }

   resource "azurerm_lb" "test" {
    name                = "loadBalancer"
    location            = azurerm_resource_group.test.location
    resource_group_name = azurerm_resource_group.test.name

    frontend_ip_configuration {
      name                 = "publicIPAddress"
      public_ip_address_id = azurerm_public_ip.test.id
    }
   }

   resource "azurerm_lb_backend_address_pool" "test" {
    resource_group_name = azurerm_resource_group.test.name
    loadbalancer_id     = azurerm_lb.test.id
    name                = "BackEndAddressPool"
   }

   resource "azurerm_network_interface" "test" {
    count               = 2
    name                = "acctni${count.index}"
    location            = azurerm_resource_group.test.location
    resource_group_name = azurerm_resource_group.test.name

    ip_configuration {
      name                          = "testConfiguration"
      subnet_id                     = azurerm_subnet.test.id
      private_ip_address_allocation = "dynamic"
      load_balancer_backend_address_pools_ids = [azurerm_lb_backend_address_pool.test.id]
    }
   }

   resource "azurerm_managed_disk" "test" {
    count                = 2
    name                 = "datadisk_existing_${count.index}"
    location             = azurerm_resource_group.test.location
    resource_group_name  = azurerm_resource_group.test.name
    storage_account_type = "Standard_LRS"
    create_option        = "Empty"
    disk_size_gb         = "1023"
   }

   resource "azurerm_availability_set" "avset" {
    name                         = "avset"
    location                     = azurerm_resource_group.test.location
    resource_group_name          = azurerm_resource_group.test.name
    platform_fault_domain_count  = 2
    platform_update_domain_count = 2
    managed                      = true
   }

   resource "azurerm_virtual_machine" "test" {
    count                 = 2
    name                  = "acctvm${count.index}"
    location              = azurerm_resource_group.test.location
    availability_set_id   = azurerm_availability_set.avset.id
    resource_group_name   = azurerm_resource_group.test.name
    network_interface_ids = [element(azurerm_network_interface.test.*.id, count.index)]
    vm_size               = "Standard_DS1_v2"

    # Uncomment this line to delete the OS disk automatically when deleting the VM
    # delete_os_disk_on_termination = true

    # Uncomment this line to delete the data disks automatically when deleting the VM
    # delete_data_disks_on_termination = true

    storage_image_reference {
      publisher = "Canonical"
      offer     = "UbuntuServer"
      sku       = "16.04-LTS"
      version   = "latest"
    }

    storage_os_disk {
      name              = "myosdisk${count.index}"
      caching           = "ReadWrite"
      create_option     = "FromImage"
      managed_disk_type = "Standard_LRS"
    }

    # Optional data disks
    storage_data_disk {
      name              = "datadisk_new_${count.index}"
      managed_disk_type = "Standard_LRS"
      create_option     = "Empty"
      lun               = 0
      disk_size_gb      = "1023"
    }

    storage_data_disk {
      name            = element(azurerm_managed_disk.test.*.name, count.index)
      managed_disk_id = element(azurerm_managed_disk.test.*.id, count.index)
      create_option   = "Attach"
      lun             = 1
      disk_size_gb    = element(azurerm_managed_disk.test.*.disk_size_gb, count.index)
    }

    os_profile {
      computer_name  = "hostname"
      admin_username = "testadmin"
      admin_password = "Password1234!"
    }

    os_profile_linux_config {
      disable_password_authentication = false
    }

    tags = {
      environment = "staging"
    }
   }
   ```

## <a name="3-initialize-terraform"></a>3. Initialiser Terraform 

La [commande terraform init](https://www.terraform.io/docs/commands/init.html) est utilisée pour initialiser un répertoire qui contient les fichiers de configuration Terraform - les fichiers que vous avez créés dans les sections précédentes. Nous vous recommandons de toujours exécuter la commande `terraform init` après l’écriture d’une nouvelle configuration Terraform. 

> [!TIP]
> La commande `terraform init` est idempotente, c'est-à-dire qu’elle peut être appelée à plusieurs reprises et produire le même résultat. Par conséquent, si vous travaillez dans un environnement de collaboration et que vous pensez que les fichiers de configuration ont été modifiés, il est toujours conseillé d’appeler la commande `terraform init` avant d’exécuter ou d’appliquer un plan.

Pour initialiser Terraform, exécutez la commande suivante :

  ```bash
  terraform init
  ```

  ![Initialisation Terraform](media/terraform-create-vm-cluster-with-infrastructure/terraform-init.png)

## <a name="4-create-a-terraform-execution-plan"></a>4. Créer un plan d’exécution Terraform

La [commande terraform plan](https://www.terraform.io/docs/commands/plan.html) est utilisée pour créer un plan d’exécution. Pour générer un plan d’exécution, Terraform regroupe tous les fichiers `.tf` dans le répertoire actif. 

Le paramètre [-out](https://www.terraform.io/docs/commands/plan.html#out-path) enregistre le plan d’exécution dans un fichier de sortie. Cette fonctionnalité résout les problèmes d’accès simultané courants dans les environnements à plusieurs développeurs. L’un de ces problèmes résolus par le fichier de sortie est le scénario suivant :

1. Le développeur 1 crée le fichier de configuration.
1. Le développeur 2 modifie le fichier de configuration.
1. Le développeur 1 applique (exécute) le fichier de configuration.
1. Le développeur 1 obtient des résultats inattendus, car il ne sait pas que le développeur 2 a modifié la configuration.

En spécifiant un fichier de sortie, le développeur 1 empêche le développeur 2 de l’affecter. 

Si vous n’avez pas besoin d’enregistrer votre plan d’exécution, exécutez la commande suivante :

  ```bash
  terraform plan
  ```

Si vous devez enregistrer votre plan d’exécution, exécutez la commande suivante. Remplacez les espaces réservés par les valeurs appropriées pour votre environnement.

  ```bash
  terraform plan -out=<path>
  ```

Un autre paramètre utile est [-var-file](https://www.terraform.io/docs/commands/plan.html#var-file-foo).

Par défaut, Terraform a essayé de trouver votre fichier de variables comme suit :
- Fichier nommé `terraform.tfvars`
- Fichier nommé à l’aide du modèle suivant : `*.auto.tfvars`

Toutefois, votre fichier de variables n’a pas besoin de suivre l’une des deux conventions précédentes. Dans ce cas, spécifiez le nom de votre fichier de variables avec le paramètre `-var-file`, sans inclure l’extension du fichier. L’exemple suivant illustre ce point :

```hcl
terraform plan -var-file <my-variables-file>
```

Terraform détermine les actions nécessaires pour atteindre l’état spécifié dans le fichier de configuration.

![Création d’un plan d’exécution Terraform](media/terraform-create-vm-cluster-with-infrastructure/terraform-plan.png)

## <a name="5-apply-the-terraform-execution-plan"></a>5. Appliquer le plan d’exécution Terraform

La dernière étape de ce didacticiel est d’utiliser la [commande terraform apply](https://www.terraform.io/docs/commands/apply.html) pour appliquer l’ensemble d’actions générées par la commande `terraform plan`.

Si vous souhaitez appliquer le dernier plan d’exécution, exécutez la commande suivante :

  ```bash
  terraform apply
  ```

Si vous souhaitez appliquer un plan d’exécution précédemment enregistré, exécutez la commande suivante. Remplacez les espaces réservés par les valeurs appropriées pour votre environnement :

  ```bash
  terraform apply <path>
  ```

![Application d’un plan d’exécution Terraform](media/terraform-create-vm-cluster-with-infrastructure/terraform-apply.png)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"] 
> [Créer un groupe identique de machines virtuelles Azure à l’aide de Terraform](terraform-create-vm-scaleset-network-disks-hcl.md)
