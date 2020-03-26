---
title: 'Tutoriel : Créer un groupe identique de machines virtuelles Azure à partir d’une image personnalisée Packer à l’aide de Terraform'
description: Utilisez Terraform pour effectuer la configuration et gérer les versions d’un groupe de machines virtuelles Azure identiques à partir d’une image personnalisée générée par Packer (comprenant un réseau virtuel et des disques attachés gérés).
ms.topic: tutorial
ms.date: 11/07/2019
ms.openlocfilehash: 92a8221d625f8b6b73343f74b85fdfcf5e578b23
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "77472200"
---
# <a name="tutorial-create-an-azure-virtual-machine-scale-set-from-a-packer-custom-image-by-using-terraform"></a>Tutoriel : Créer un groupe identique de machines virtuelles Azure à partir d’une image personnalisée Packer à l’aide de Terraform

Dans ce tutoriel, vous allez utiliser [Terraform](https://www.terraform.io/) pour créer et déployer un [groupe identique de machines virtuelles Azure](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview), créé avec une image personnalisée générée à l’aide de [Packer](https://www.packer.io/intro/index.html) et comprenant des disques managés qui utilisent le [langage de configuration Hashicorp](https://www.terraform.io/docs/configuration/syntax.html) (HCL). 

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Configurer votre déploiement Terraform.
> * Utiliser des variables et des sorties pour le déploiement Terraform.
> * Créer et déployer une infrastructure réseau.
> * Créer une image de machine virtuelle personnalisée à l’aide de Packer.
> * Créer et déployer un groupe identique de machines virtuelles à l’aide de l’image personnalisée.
> * Créer et déployer un jumpbox.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

- **Terraform** : [Installez Terraform et configurez l’accès à Azure](terraform-install-configure.md).
- **Paire de clés SSH** : [Créez une paire de clés SSH](/azure/virtual-machines/linux/mac-create-ssh-keys).
- **Packer** :  [Installez Packer](https://www.packer.io/docs/install/index.html).

## <a name="create-the-file-structure"></a>Créer la structure de fichiers

Créez trois nouveaux fichiers dans un répertoire vide avec les noms suivants :

- `variables.tf`: Ce fichier contient les valeurs des variables utilisées dans le modèle.
- `output.tf`: Ce fichier décrit les paramètres qui s’affichent après le déploiement.
- `vmss.tf`: Ce fichier contient le code de l’infrastructure que vous déployez.

##  <a name="create-the-variables"></a>Créer les variables 

Cette étape vous permet de définir les variables qui personnalisent les ressources créées par Terraform.

Modifiez le fichier `variables.tf`, copiez le code suivant, puis enregistrez les modifications.

```hcl
variable "location" {
  description = "The location where resources are created"
  default     = "East US"
}

variable "resource_group_name" {
  description = "The name of the resource group in which the resources are created"
  default     = ""
}

```

> [!NOTE]
> La valeur par défaut de la variable resource_group_name n’est pas définie. Définissez votre propre valeur.

Enregistrez le fichier .

Lorsque vous déployez votre modèle Terraform, vous devez obtenir le nom de domaine complet utilisé pour accéder à l’application. Utilisez le type de ressource `output` de Terraform et dérivez la propriété `fqdn` de la ressource. 

Modifiez le fichier `output.tf` et copiez le code suivant pour exposer le nom de domaine complet pour les machines virtuelles. 

```hcl 
output "vmss_public_ip" {
    value = azurerm_public_ip.vmss.fqdn
}
```

## <a name="define-the-network-infrastructure-in-a-template"></a>Définir l’infrastructure réseau dans un modèle 

Cette étape vous permet de créer l’infrastructure réseau suivante dans un nouveau groupe de ressources Azure : 
  - Un réseau virtuel avec l’espace d’adressage 10.0.0.0/16.
  - Un sous-réseau avec l’espace d’adressage 10.0.2.0/24.
  - Deux adresses IP publiques. L’une est utilisée par l’équilibreur de charge de groupe identique de machines virtuelles. L’autre est utilisée pour la connexion au jumpbox SSH.

Vous avez également besoin d’un groupe de ressources où sont créées toutes les ressources. 

Modifiez et copiez le code suivant dans le fichier `vmss.tf` : 

```hcl

resource "azurerm_resource_group" "vmss" {
  name     = var.resource_group_name
  location = var.location

  tags {
    environment = "codelab"
  }
}

resource "azurerm_virtual_network" "vmss" {
  name                = "vmss-vnet"
  address_space       = ["10.0.0.0/16"]
  location            = var.location
  resource_group_name = azurerm_resource_group.vmss.name

  tags {
    environment = "codelab"
  }
}

resource "azurerm_subnet" "vmss" {
  name                 = "vmss-subnet"
  resource_group_name  = azurerm_resource_group.vmss.name
  virtual_network_name = azurerm_virtual_network.vmss.name
  address_prefix       = "10.0.2.0/24"
}

resource "azurerm_public_ip" "vmss" {
  name                         = "vmss-public-ip"
  location                     = var.location
  resource_group_name          = azurerm_resource_group.vmss.name
  allocation_method            = "static"
  domain_name_label            = azurerm_resource_group.vmss.name

  tags {
    environment = "codelab"
  }
}

``` 

> [!NOTE]
> Balisez les ressources qui sont déployées dans Azure pour faciliter leur identification à l’avenir.

## <a name="create-the-network-infrastructure"></a>Créer l’infrastructure réseau

Initialisez l’environnement Terraform en exécutant la commande suivante dans le répertoire où vous avez créé les fichiers `.tf` :

```bash
terraform init 
```
 
Les plug-ins du fournisseur sont téléchargés à partir du registre Terraform dans le dossier `.terraform` du répertoire où vous avez exécuté la commande.

Exécutez la commande suivante pour déployer l’infrastructure dans Azure.

```bash
terraform apply
```

Vérifiez que le nom de domaine complet de l’adresse IP publique correspond à votre configuration.

![Nom de domaine complet Terraform du groupe identique de machines virtuelles pour l’adresse IP publique](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step4-fqdn.png)

Le groupe de ressources contient les ressources suivantes :

![Ressources réseau Terraform du groupe de machines virtuelles identiques](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step4-rg.png)


## <a name="create-an-azure-image-by-using-packer"></a>Créer une image Azure à l’aide de Packer
Créez une image Linux personnalisée en suivant la procédure décrite dans le tutoriel [Comment utiliser Packer pour créer des images de machines virtuelles Linux dans Azure](/azure/virtual-machines/linux/build-image-with-packer).
 
Suivez le tutoriel pour créer une image Ubuntu déprovisionnée avec Nginx installé.

![Une fois l’image Packer créée, vous disposez d’une image](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/packerimagecreated.png)

> [!NOTE]
> Dans le cadre de ce tutoriel, une commande est exécutée dans l’image Packer pour installer Nginx. Vous pouvez également exécuter votre propre script lors de la procédure de création.

## <a name="edit-the-infrastructure-to-add-the-virtual-machine-scale-set"></a>Modifier l’infrastructure pour ajouter le groupe de machines virtuelles identiques

Cette étape vous permet de créer les ressources suivantes sur le réseau qui a déjà été déployé :
- Un équilibreur de charge Azure pour prendre en charge l’application. Attachez-le à l’adresse IP publique déployée précédemment.
- Un équilibreur de charge Azure et des règles pour prendre en charge l’application. Attachez-le à l’adresse IP publique configurée précédemment.
- Un pool d’adresses back-end Azure. Affectez-le à l’équilibreur de charge.
- Un port de sonde d’intégrité utilisé par l’application et configuré sur l’équilibreur de charge.
- Un groupe identique de machines virtuelles qui se trouve derrière l’équilibreur de charge et s’exécute sur le réseau virtuel déployé précédemment.
- [Nginx](https://nginx.org/) sur les nœuds du groupe identique de machines virtuelles installé à partir d’une image personnalisée.


Ajoutez le code suivant à la fin du fichier `vmss.tf`.

```hcl

resource "azurerm_lb" "vmss" {
  name                = "vmss-lb"
  location            = var.location
  resource_group_name = azurerm_resource_group.vmss.name

  frontend_ip_configuration {
    name                 = "PublicIPAddress"
    public_ip_address_id = azurerm_public_ip.vmss.id
  }

  tags {
    environment = "codelab"
  }
}

resource "azurerm_lb_backend_address_pool" "bpepool" {
  resource_group_name = azurerm_resource_group.vmss.name
  loadbalancer_id     = azurerm_lb.vmss.id
  name                = "BackEndAddressPool"
}

resource "azurerm_lb_probe" "vmss" {
  resource_group_name = azurerm_resource_group.vmss.name
  loadbalancer_id     = azurerm_lb.vmss.id
  name                = "ssh-running-probe"
  port                = var.application_port
}

resource "azurerm_lb_rule" "lbnatrule" {
  resource_group_name            = azurerm_resource_group.vmss.name
  loadbalancer_id                = azurerm_lb.vmss.id
  name                           = "http"
  protocol                       = "Tcp"
  frontend_port                  = var.application_port
  backend_port                   = var.application_port
  backend_address_pool_id        = azurerm_lb_backend_address_pool.bpepool.id
  frontend_ip_configuration_name = "PublicIPAddress"
  probe_id                       = azurerm_lb_probe.vmss.id
}

data "azurerm_resource_group" "image" {
  name = "myResourceGroup"
}

data "azurerm_image" "image" {
  name                = "myPackerImage"
  resource_group_name = data.azurerm_resource_group.image.name
}

resource "azurerm_virtual_machine_scale_set" "vmss" {
  name                = "vmscaleset"
  location            = var.location
  resource_group_name = azurerm_resource_group.vmss.name
  upgrade_policy_mode = "Manual"

  sku {
    name     = "Standard_DS1_v2"
    tier     = "Standard"
    capacity = 2
  }

  storage_profile_image_reference {
    id=data.azurerm_image.image.id
  }

  storage_profile_os_disk {
    name              = ""
    caching           = "ReadWrite"
    create_option     = "FromImage"
    managed_disk_type = "Standard_LRS"
  }

  storage_profile_data_disk {
    lun          = 0
    caching        = "ReadWrite"
    create_option  = "Empty"
    disk_size_gb   = 10
  }

  os_profile {
    computer_name_prefix = "vmlab"
    admin_username       = "azureuser"
    admin_password       = "Passwword1234"
  }

  os_profile_linux_config {
    disable_password_authentication = true

    ssh_keys {
      path     = "/home/azureuser/.ssh/authorized_keys"
      key_data = file("~/.ssh/id_rsa.pub")
    }
  }

  network_profile {
    name    = "terraformnetworkprofile"
    primary = true

    ip_configuration {
      name                                   = "IPConfiguration"
      subnet_id                              = azurerm_subnet.vmss.id
      load_balancer_backend_address_pool_ids = [azurerm_lb_backend_address_pool.bpepool.id]
      primary = true
    }
  }
  
  tags {
    environment = "codelab"
  }
}

```

Personnalisez le déploiement en ajoutant le code suivant à `variables.tf` :

```hcl
variable "application_port" {
    description = "The port that you want to expose to the external load balancer"
    default     = 80
}

variable "admin_password" {
    description = "Default password for admin"
    default = "Passwwoord11223344"
}
``` 


## <a name="deploy-the-virtual-machine-scale-set-in-azure"></a>Déployer le groupe de machines virtuelles identiques dans Azure

Exécutez la commande suivante pour visualiser le déploiement du groupe de machines virtuelles identiques :

```bash
terraform plan
```

La sortie de la commande doit se présenter comme suit :

![Ajout par Terraform d’un plan de groupes de machines virtuelles identiques](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step6-plan.png)

Déployez les ressources supplémentaires dans Azure : 

```bash
terraform apply 
```

Le contenu du groupe de ressources ressemble à l’image suivante :

![Groupe de ressources du groupe de machines virtuelles identiques Terraform](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step6-apply.png)

Ouvrez un navigateur et connectez-vous au nom de domaine complet qui a été retourné par la commande. 


## <a name="add-a-jumpbox-to-the-existing-network"></a>Ajouter une jumpbox au réseau existant 

Cette étape facultative permet d’utiliser une jumbox pour autoriser un accès SSH aux instances du groupe de machines virtuelles identiques.

Ajoutez les ressources suivantes à votre déploiement existant :
- Une interface réseau connectée au même sous-réseau que le groupe identique de machines virtuelles
- Une machine virtuelle connectée avec cette interface réseau

Ajoutez le code suivant à la fin du fichier `vmss.tf` :

```hcl 
resource "azurerm_public_ip" "jumpbox" {
  name                         = "jumpbox-public-ip"
  location                     = var.location
  resource_group_name          = azurerm_resource_group.vmss.name
  allocation_method            = "static"
  domain_name_label            = "${azurerm_resource_group.vmss.name}-ssh"

  tags {
    environment = "codelab"
  }
}

resource "azurerm_network_interface" "jumpbox" {
  name                = "jumpbox-nic"
  location            = var.location
  resource_group_name = azurerm_resource_group.vmss.name

  ip_configuration {
    name                          = "IPConfiguration"
    subnet_id                     = azurerm_subnet.vmss.id
    private_ip_address_allocation = "dynamic"
    public_ip_address_id          = azurerm_public_ip.jumpbox.id
  }

  tags {
    environment = "codelab"
  }
}

resource "azurerm_virtual_machine" "jumpbox" {
  name                  = "jumpbox"
  location              = var.location
  resource_group_name   = azurerm_resource_group.vmss.name
  network_interface_ids = [azurerm_network_interface.jumpbox.id]
  vm_size               = "Standard_DS1_v2"

  storage_image_reference {
    publisher = "Canonical"
    offer     = "UbuntuServer"
    sku       = "16.04-LTS"
    version   = "latest"
  }

  storage_os_disk {
    name              = "jumpbox-osdisk"
    caching           = "ReadWrite"
    create_option     = "FromImage"
    managed_disk_type = "Standard_LRS"
  }

  os_profile {
    computer_name  = "jumpbox"
    admin_username = "azureuser"
    admin_password = "Password1234!"
  }

  os_profile_linux_config {
    disable_password_authentication = true

    ssh_keys {
      path     = "/home/azureuser/.ssh/authorized_keys"
      key_data = file("~/.ssh/id_rsa.pub")
    }
  }

  tags {
    environment = "codelab"
  }
}
```

Modifiez `outputs.tf` et ajoutez le code suivant pour afficher le nom d’hôte du jumpbox lorsque le déploiement est terminé :

```
output "jumpbox_public_ip" {
    value = azurerm_public_ip.jumpbox.fqdn
}
```

## <a name="deploy-the-jumpbox"></a>Déployer la jumpbox

Déployez la jumpbox.

```bash
terraform apply 
```

Une fois le déploiement terminé, le contenu du groupe de ressources ressemble à l’image suivante :

![Groupe de ressources du groupe de machines virtuelles identiques Terraform](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-create-vmss-step8.png)

> [!NOTE]
> La connexion avec un mot de passe est désactivée sur le jumpbox et le groupe identique de machines virtuelles que vous avez déployés. Connectez-vous avec SSH pour accéder aux machines virtuelles.

## <a name="clean-up-the-environment"></a>Nettoyer l’environnement

La commande suivante supprime les ressources créées dans ce didacticiel :

```bash
terraform destroy
```

Entrez *Oui* quand vous êtes invité à confirmer la suppression des ressources. Le processus de destruction peut prendre plusieurs minutes.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"] 
> [En savoir plus sur l’utilisation de Terraform dans Azure](/azure/terraform)
