---
title: Utiliser Terraform pour créer un groupe de machines virtuelles identiques Azure
description: Didacticiel sur l’utilisation de Terraform pour configurer un groupe de machines virtuelles identiques Azure avec un réseau virtuel et des disques attachés gérés, et lui attribuer une version
services: terraform
ms.service: terraform
keywords: terraform, devops, machine virtuelle, Azure, groupe identique, réseau, stockage, modules
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 06/04/2018
ms.openlocfilehash: 7ae97274b03dda4dcf5150c8faacc7d406dad9fd
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49389613"
---
# <a name="use-terraform-to-create-an-azure-virtual-machine-scale-set"></a>Utiliser Terraform pour créer un groupe de machines virtuelles identiques Azure

[Les groupes de machines virtuelles identiques Azure](/azure/virtual-machine-scale-sets) vous permettent de créer et de gérer un groupe de machines virtuelles identiques dont la charge est équilibrée et où le nombre d’instances de machine virtuelle peut automatiquement augmenter ou diminuer en fonction de la demande ou d’une calendrier défini. 

Dans ce didacticiel, vous allez apprendre à utiliser [Azure Cloud Shell](/azure/cloud-shell/overview) pour effectuer les tâches suivantes :

> [!div class="checklist"]
> * Configurer un déploiement Terraform
> * Utiliser des variables et des sorties pour le déploiement Terraform 
> * Créer et déployer une infrastructure réseau
> * Créer et déployer un groupe de machines virtuelles identiques et le joindre au réseau
> * Créer et déployer une jumpbox pour la connexion aux machines virtuelles via SSH

> [!NOTE]
> La version la plus récente des fichiers config de Terraform utilisés dans cet article se trouve dans le [référentiel Awesome Terraform sur Github](https://github.com/Azure/awesome-terraform/tree/master/codelab-vmss).

## <a name="prerequisites"></a>Prérequis

- **Abonnement Azure** : si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) avant de commencer.

- **Installation de Terraform** : suivez les instructions de l’article [Installer et configurer Terraform pour approvisionner les machines virtuelles et d’autres infrastructures dans Azure](/azure/virtual-machines/linux/terraform-install-configure).

- **Création d’une paire de clés SSH** : si vous n’avez pas encore de paire de clés SSH, suivez les instructions dans l’article [Étapes rapides : Créer et utiliser une paire de clés publique et privée SSH pour les machines virtuelles Linux dans Azure](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys).

## <a name="create-the-directory-structure"></a>Créer la structure de répertoire

1. Accédez au [portail Azure](http://portal.azure.com).

1. Ouvrez [Azure Cloud Shell](/azure/cloud-shell/overview). Si vous n’avez pas sélectionné d’environnement précédemment, choisissez **Bash** comme votre environnement.

    ![Invite Cloud Shell](./media/terraform-create-vm-scaleset-network-disks-hcl/azure-portal-cloud-shell-button-min.png)

1. Remplacez le répertoire par le répertoire `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Créez un répertoire nommé `vmss`.

    ```bash
    mkdir vmss
    ```

1. Remplacez les répertoires par le nouveau répertoire :

    ```bash
    cd vmss
    ```

## <a name="create-the-variables-definitions-file"></a>Créer le fichier contenant les définitions des variables
Dans cette section, vous allez définir les variables qui personnalisent les ressources créées par Terraform.

Dans l’instance Azure Cloud Shell, effectuez les étapes suivantes :

1. Créez un fichier appelé `variables.tf`.

    ```bash
    vi variables.tf
    ```

1. Passez en mode insertion en sélectionnant la touche I.

1. Collez le code suivant dans l’éditeur :

  ```JSON
  variable "location" {
    description = "The location where resources will be created"
  }

  variable "tags" {
    description = "A map of the tags to use for the resources that are deployed"
    type        = "map"

    default = {
      environment = "codelab"
    }
  }

  variable "resource_group_name" {
    description = "The name of the resource group in which the resources will be created"
    default     = "myResourceGroup"
  }
  ```

1. Quittez le mode insertion en sélectionnant la touche Échap.

1. Enregistrez le fichier et quittez l’éditeur vi en entrant la commande suivante :

    ```bash
    :wq
    ```

## <a name="create-the-output-definitions-file"></a>Créer le fichier contenant les définitions de la sortie
Dans cette section, vous allez créer le fichier qui décrit la sortie après le déploiement.

Dans l’instance Azure Cloud Shell, effectuez les étapes suivantes :

1. Créez un fichier appelé `output.tf`.

    ```bash
    vi output.tf
    ```

1. Passez en mode insertion en sélectionnant la touche I.

1. Collez le code suivant dans l’éditeur pour exposer le nom de domaine complet pour les machines virtuelles. :

  ```JSON
    output "vmss_public_ip" {
        value = "${azurerm_public_ip.vmss.fqdn}"
    }
  ```

1. Quittez le mode insertion en sélectionnant la touche Échap.

1. Enregistrez le fichier et quittez l’éditeur vi en entrant la commande suivante :

    ```bash
    :wq
    ```

## <a name="define-the-network-infrastructure-in-a-template"></a>Définir l’infrastructure réseau dans un modèle
Dans cette section, vous allez créer l’infrastructure réseau suivante dans un nouveau groupe de ressources Azure : 

  - Un réseau virtuel avec l’espace d’adressage 10.0.0.0/16 
  - Un sous-réseau avec l’espace d’adressage 10.0.2.0/24
  - Deux adresses IP publiques. Une est utilisée par l’équilibrage de charge du groupe de machines virtuelles identiques et l’autre est utilisée pour la connexion à la jumpbox SSH.

Dans l’instance Azure Cloud Shell, effectuez les étapes suivantes :

1. Créez un fichier nommé `vmss.tf` pour décrire l’infrastructure du groupe de machines virtuelles identiques.

    ```bash
    vi vmss.tf
    ```

1. Passez en mode insertion en sélectionnant la touche I.

1. Collez le code suivant à la fin du fichier pour exposer le nom de domaine complet pour les machines virtuelles. 

  ```JSON
  resource "azurerm_resource_group" "vmss" {
    name     = "${var.resource_group_name}"
    location = "${var.location}"
    tags     = "${var.tags}"
  }

  resource "random_string" "fqdn" {
    length  = 6
    special = false
    upper   = false
    number  = false
  }

  resource "azurerm_virtual_network" "vmss" {
    name                = "vmss-vnet"
    address_space       = ["10.0.0.0/16"]
    location            = "${var.location}"
    resource_group_name = "${azurerm_resource_group.vmss.name}"
    tags                = "${var.tags}"
  }

  resource "azurerm_subnet" "vmss" {
    name                 = "vmss-subnet"
    resource_group_name  = "${azurerm_resource_group.vmss.name}"
    virtual_network_name = "${azurerm_virtual_network.vmss.name}"
    address_prefix       = "10.0.2.0/24"
  }

  resource "azurerm_public_ip" "vmss" {
    name                         = "vmss-public-ip"
    location                     = "${var.location}"
    resource_group_name          = "${azurerm_resource_group.vmss.name}"
    public_ip_address_allocation = "static"
    domain_name_label            = "${random_string.fqdn.result}"
    tags                         = "${var.tags}"
  }
  ```

1. Quittez le mode insertion en sélectionnant la touche Échap.

1. Enregistrez le fichier et quittez l’éditeur vi en entrant la commande suivante :

  ```bash
  :wq
  ```

## <a name="provision-the-network-infrastructure"></a>Approvisionner l’infrastructure réseau
Dans le répertoire où vous avez créé les fichiers config (.tf), utilisez Azure Cloud Shell pour effectuer les étapes suivantes :

1. Initialisez Terraform.

  ```bash
  terraform init 
  ```

1. Exécutez la commande suivante pour déployer l’infrastructure définie dans Azure.

  ```bash
  terraform apply
  ```

  Terraform vous invite à entrer une valeur de type « emplacement », car la variable **location** est définie dans `variables.tf` mais n’est jamais renseignée. Vous pouvez entrer n’importe quel emplacement valide, par exemple « USA Ouest », puis sélectionnez Entrée. (Utilisez des parenthèses autour d’une valeur avec des espaces.)

1. Terraform imprime la sortie comme défini dans le fichier `output.tf`. La capture d’écran suivante montre que le nom de domaine complet est au format suivant &lt;id>.&lt;location>.cloudapp.azure.com. La valeur d’id correspond à une valeur calculée, tandis que l’emplacement est la valeur que vous fournissez lors de l’exécution Terraform.

  ![Nom de domaine complet du groupe de machines virtuelles identiques Terraform pour l’adresse IP publique](./media/terraform-create-vm-scaleset-network-disks-hcl/fqdn.png)

1. Dans le menu principal du Portail Azure, sélectionnez **Groupes de ressources**.

1. Dans l’onglet **Groupes de ressources**, sélectionnez **myResourceGroup** pour afficher les ressources qui ont été créées par Terraform.
  ![Ressources réseau du groupe de machines virtuelles identiques](./media/terraform-create-vm-scaleset-network-disks-hcl/resource-group-resources.png)

## <a name="add-a-virtual-machine-scale-set"></a>Ajouter un groupe de machines virtuelles identiques

Dans cette section, vous allez apprendre à ajouter les ressources suivantes au modèle :

- Un équilibreur de charge Azure et des règles pour prendre en charge l’application et l’attacher à l’adresse IP publique configurée précédemment dans cet article
- Un pool d’adresses principales Azure qui est ensuite assigné à l’équilibreur de charge 
- Un port de sonde d’intégrité utilisé par l’application et configuré sur l’équilibrage de charge 
- Un groupe de machines virtuelles identiques derrière l’équilibreur de charge, exécuté sur le réseau virtuel déployé précédemment dans cet article
- [Nginx](http://nginx.org/) sur les nœuds du groupe de machines virtuelles identiques à l’aide de [cloud-init](http://cloudinit.readthedocs.io/en/latest/).

Dans Cloud Shell, effectuez la procédure suivante :

1. Ouvrez le fichier config `vmss.tf`.

  ```bash
  vi vmss.tf
  ```

1. Accédez à la fin du fichier et entrez dans le mode ajout en sélectionnant la touche A.

1. Copiez le code suivant à la fin du fichier :

  ```JSON
  resource "azurerm_lb" "vmss" {
    name                = "vmss-lb"
    location            = "${var.location}"
    resource_group_name = "${azurerm_resource_group.vmss.name}"

    frontend_ip_configuration {
      name                 = "PublicIPAddress"
      public_ip_address_id = "${azurerm_public_ip.vmss.id}"
    }

    tags = "${var.tags}"
  }

  resource "azurerm_lb_backend_address_pool" "bpepool" {
    resource_group_name = "${azurerm_resource_group.vmss.name}"
    loadbalancer_id     = "${azurerm_lb.vmss.id}"
    name                = "BackEndAddressPool"
  }

  resource "azurerm_lb_probe" "vmss" {
    resource_group_name = "${azurerm_resource_group.vmss.name}"
    loadbalancer_id     = "${azurerm_lb.vmss.id}"
    name                = "ssh-running-probe"
    port                = "${var.application_port}"
  }

  resource "azurerm_lb_rule" "lbnatrule" {
      resource_group_name            = "${azurerm_resource_group.vmss.name}"
      loadbalancer_id                = "${azurerm_lb.vmss.id}"
      name                           = "http"
      protocol                       = "Tcp"
      frontend_port                  = "${var.application_port}"
      backend_port                   = "${var.application_port}"
      backend_address_pool_id        = "${azurerm_lb_backend_address_pool.bpepool.id}"
      frontend_ip_configuration_name = "PublicIPAddress"
      probe_id                       = "${azurerm_lb_probe.vmss.id}"
  }

  resource "azurerm_virtual_machine_scale_set" "vmss" {
    name                = "vmscaleset"
    location            = "${var.location}"
    resource_group_name = "${azurerm_resource_group.vmss.name}"
    upgrade_policy_mode = "Manual"

    sku {
      name     = "Standard_DS1_v2"
      tier     = "Standard"
      capacity = 2
    }

    storage_profile_image_reference {
      publisher = "Canonical"
      offer     = "UbuntuServer"
      sku       = "16.04-LTS"
      version   = "latest"
    }

    storage_profile_os_disk {
      name              = "osdisk"
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
      admin_username       = "${var.admin_user}"
      admin_password       = "${var.admin_password}"
      custom_data          = "${file("web.conf")}"
    }

    os_profile_linux_config {
      disable_password_authentication = false
    }

    network_profile {
      name    = "terraformnetworkprofile"
      primary = true

      ip_configuration {
        name                                   = "IPConfiguration"
        subnet_id                              = "${azurerm_subnet.vmss.id}"
        load_balancer_backend_address_pool_ids = ["${azurerm_lb_backend_address_pool.bpepool.id}"]
      }
    }

    tags = "${var.tags}"
}
  ```

1. Quittez le mode insertion en sélectionnant la touche Échap.

1. Enregistrez le fichier et quittez l’éditeur vi en entrant la commande suivante :

    ```bash
    :wq
    ```

1. Créez un fichier nommé `web.conf` pour prendre en charge la configuration cloud-init des machines virtuelles appartenant au groupe identique. 

    ```bash
    vi web.conf
    ```

1. Passez en mode insertion en sélectionnant la touche I.

1. Collez le code suivant dans l’éditeur :

  ```JSON
  #cloud-config
  packages:
    - nginx
  ```

1. Quittez le mode insertion en sélectionnant la touche Échap.

1. Enregistrez le fichier et quittez l’éditeur vi en entrant la commande suivante :

    ```bash
    :wq
    ```

1. Ouvrez le fichier config `variables.tf`.

  ```bash
  vi variables.tf
  ```

1. Accédez à la fin du fichier et entrez dans le mode ajout en sélectionnant la touche A.

1. Personnalisez le déploiement en collant le code suivant à la fin du fichier :

  ```JSON
  variable "application_port" {
      description = "The port that you want to expose to the external load balancer"
      default     = 80
  }

  variable "admin_user" {
      description = "User name to use as the admin account on the VMs that will be part of the VM Scale Set"
      default     = "azureuser"
  }

  variable "admin_password" {
      description = "Default password for admin account"
  }
  ``` 

1. Quittez le mode insertion en sélectionnant la touche Échap.

1. Enregistrez le fichier et quittez l’éditeur vi en entrant la commande suivante :

    ```bash
    :wq
    ```

1. Créez un plan Terraform pour visualiser le déploiement du groupe de machines virtuelles identiques. (Vous devez spécifier un mot de passe de votre choix, ainsi que l’emplacement de vos ressources.)

  ```bash
  terraform plan
  ```

  La sortie de la commande doit ressembler à la capture d’écran suivante :

  ![Sortie de la création du groupe de machines virtuelles identiques](./media/terraform-create-vm-scaleset-network-disks-hcl/add-mvss-plan.png)

1. Déployez les nouvelles ressources dans Azure.

  ```bash
  terraform apply 
  ```

  La sortie de la commande doit ressembler à la capture d’écran suivante :

  ![Groupe de ressources du groupe de machines virtuelles identiques Terraform](./media/terraform-create-vm-scaleset-network-disks-hcl/resource-group-contents.png)

1. Ouvrez un navigateur et connectez-vous au nom de domaine complet qui a été retourné par la commande. 

  ![Résultats après avoir recherché le nom de domaine complet](./media/terraform-create-vm-scaleset-network-disks-hcl/browser-fqdn.png)

## <a name="add-an-ssh-jumpbox"></a>Ajouter une jumpbox SSH
Une *jumpbox* SSH est un serveur unique dans lequel vous passez rapidement pour accéder à d’autres serveurs du réseau. Cette étape vous permet de configurer les ressources suivantes :

- Une interface réseau (ou jumpbox) connectée au même sous-réseau que le groupe de machines virtuelles identiques.

- Une machine virtuelle connectée avec cette interface réseau. Cette « jumpbox » est accessible à distance. Une fois connecté, vous pouvez utiliser SSH pour accéder à n’importe quelle machine virtuelle du groupe de machines virtuelles identiques.

1. Ouvrez le fichier config `vmss.tf`.

  ```bash
  vi vmss.tf
  ```

1. Accédez à la fin du fichier et entrez dans le mode ajout en sélectionnant la touche A.

1. Copiez le code suivant à la fin du fichier :

  ```JSON
  resource "azurerm_public_ip" "jumpbox" {
    name                         = "jumpbox-public-ip"
    location                     = "${var.location}"
    resource_group_name          = "${azurerm_resource_group.vmss.name}"
    public_ip_address_allocation = "static"
    domain_name_label            = "${random_string.fqdn.result}-ssh"
    tags                         = "${var.tags}"
  }

  resource "azurerm_network_interface" "jumpbox" {
    name                = "jumpbox-nic"
    location            = "${var.location}"
    resource_group_name = "${azurerm_resource_group.vmss.name}"

    ip_configuration {
      name                          = "IPConfiguration"
      subnet_id                     = "${azurerm_subnet.vmss.id}"
      private_ip_address_allocation = "dynamic"
      public_ip_address_id          = "${azurerm_public_ip.jumpbox.id}"
    }

    tags = "${var.tags}"
  }

  resource "azurerm_virtual_machine" "jumpbox" {
    name                  = "jumpbox"
    location              = "${var.location}"
    resource_group_name   = "${azurerm_resource_group.vmss.name}"
    network_interface_ids = ["${azurerm_network_interface.jumpbox.id}"]
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
      admin_username = "${var.admin_user}"
      admin_password = "${var.admin_password}"
    }

    os_profile_linux_config {
      disable_password_authentication = false
    }

    tags = "${var.tags}"
  }
  ```

1. Ouvrez le fichier config `output.tf`.

  ```bash
  vi output.tf
  ```

1. Accédez à la fin du fichier et entrez dans le mode ajout en sélectionnant la touche A.

1. Collez le code suivant à la fin du fichier pour afficher le nom d’hôte de la jumpbox lorsque le déploiement est terminé :

  ```
  output "jumpbox_public_ip" {
      value = "${azurerm_public_ip.jumpbox.fqdn}"
  }
  ```

1. Quittez le mode insertion en sélectionnant la touche Échap.

1. Enregistrez le fichier et quittez l’éditeur vi en entrant la commande suivante :

    ```bash
    :wq
    ```

1. Déployez la jumpbox.

  ```bash
  terraform apply 
  ```

Une fois le déploiement terminé, le contenu du groupe de ressources ressemble à ce qui est présenté dans la capture d’écran suivante :

![Groupe de ressources du groupe de machines virtuelles identiques Terraform](./media/terraform-create-vm-scaleset-network-disks-hcl/resource-group-contents-final.png)

> [!NOTE]
> La possibilité de connexion avec un mot de passe est désactivée sur la jumpbox et le groupe de machines virtuelles identiques que vous avez déployés. Connectez-vous avec SSH pour accéder aux machines virtuelles.

## <a name="environment-cleanup"></a>Suppression de l’environnement 

Pour supprimer les ressources Terraform qui ont été créées au cours de ce didacticiel, entrez la commande suivante dans Cloud Shell :

```bash
terraform destroy
```

Le processus de suppression peut prendre plusieurs minutes.

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez découvert comment utiliser Terraform pour créer un groupe de machines virtuelles identiques Azure. Pour en savoir plus sur Terraform sur Azure, consultez les ressources supplémentaires suivantes : 

 [Hub Terraform sur Microsoft.com](https://docs.microsoft.com/azure/terraform/)  
 [Documentation Terraform Azure Provider](http://aka.ms/terraform)  
 [Source Terraform Azure Provider](http://aka.ms/tfgit)  
 [Modules Terraform Azure](http://aka.ms/tfmodules)