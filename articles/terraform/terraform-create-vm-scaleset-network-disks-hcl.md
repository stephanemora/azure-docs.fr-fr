---
title: 'Tutoriel : Créer un groupe identique de machines virtuelles Azure à l’aide de Terraform'
description: Apprenez à utiliser Terraform pour configurer un groupe identique de machines virtuelles Azure et en gérer les versions.
ms.topic: tutorial
ms.date: 11/07/2019
ms.openlocfilehash: 4e445d5e6ae4b7fc4528c6d61ee2bc86870827b1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "77472228"
---
# <a name="tutorial-create-an-azure-virtual-machine-scale-set-using-terraform"></a>Tutoriel : Créer un groupe identique de machines virtuelles Azure à l’aide de Terraform

Les [groupes identiques de machines virtuelles Azure](/azure/virtual-machine-scale-sets) vous permettent de configurer des machines virtuelles identiques. Le nombre d’instances de machine virtuelle peut être ajusté en fonction de la demande ou d’une planification. Pour plus d’informations, consultez [Mettre à l’échelle automatiquement un groupe identique de machines virtuelles dans le portail Azure](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-portal).

Dans ce tutoriel, vous allez apprendre à utiliser [Azure Cloud Shell](/azure/cloud-shell/overview) pour effectuer les tâches suivantes :

> [!div class="checklist"]
> * Configurer un déploiement Terraform
> * Utiliser des variables et des sorties pour le déploiement Terraform
> * Créer et déployer une infrastructure réseau
> * Créer et déployer un groupe de machines virtuelles identiques et le joindre au réseau
> * Créer et déployer une jumpbox pour la connexion aux machines virtuelles via SSH

> [!NOTE]
> La version la plus récente des fichiers config de Terraform utilisés dans cet article se trouve dans le [référentiel Awesome Terraform sur GitHub](https://github.com/Azure/awesome-terraform/tree/master/codelab-vmss).

## <a name="prerequisites"></a>Prérequis

- **Abonnement Azure** : Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) avant de commencer.

- **Installer Terraform** : Suivez les instructions de l’article [Terraform et configuration de l’accès à Azure ](terraform-install-configure.md)

- **Créez une paire de clés SSH** : Pour plus d’informations, consultez [Comment créer et utiliser une paire de clés publique et privée SSH pour les machines virtuelles Linux dans Azure](/azure/virtual-machines/linux/mac-create-ssh-keys).

## <a name="create-the-directory-structure"></a>Créer la structure de répertoire

1. Accédez au [portail Azure](https://portal.azure.com).

1. Ouvrez [Azure Cloud Shell](/azure/cloud-shell/overview). Si vous n’avez pas sélectionné d’environnement précédemment, choisissez **Bash** comme votre environnement.

    ![Invite Cloud Shell](./media/terraform-create-vm-scaleset-network-disks-hcl/azure-portal-cloud-shell-button-min.png)

1. Déplacez-vous dans le répertoire `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Créez un répertoire nommé `vmss`.

    ```bash
    mkdir vmss
    ```

1. Déplacez-vous dans le nouveau répertoire :

    ```bash
    cd vmss
    ```

## <a name="create-the-variables-definitions-file"></a>Créer le fichier contenant les définitions des variables
Dans cette section, vous allez définir les variables qui personnalisent les ressources créées par Terraform.

Dans Azure Cloud Shell, effectuez la procédure suivante :

1. Créez un fichier appelé `variables.tf`.

    ```bash
    code variables.tf
    ```

1. Collez le code suivant dans l’éditeur :

   ```hcl
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

1. Enregistrez le fichier ( **&lt;Ctrl>S**) et quittez l’éditeur ( **&lt;Ctrl>Q**).

## <a name="create-the-output-definitions-file"></a>Créer le fichier contenant les définitions de la sortie
Dans cette section, vous allez créer le fichier qui décrit la sortie après le déploiement.

Dans Azure Cloud Shell, effectuez la procédure suivante :

1. Créez un fichier appelé `output.tf`.

    ```bash
    code output.tf
    ```

1. Collez le code suivant dans l’éditeur pour exposer le nom de domaine complet pour les machines virtuelles.
   :

   ```hcl
    output "vmss_public_ip" {
        value = azurerm_public_ip.vmss.fqdn
    }
   ```

1. Enregistrez le fichier ( **&lt;Ctrl>S**) et quittez l’éditeur ( **&lt;Ctrl>Q**).

## <a name="define-the-network-infrastructure-in-a-template"></a>Définir l’infrastructure réseau dans un modèle
Dans cette section, vous allez créer l’infrastructure réseau suivante dans un nouveau groupe de ressources Azure :

  - Un réseau virtuel avec l’espace d’adressage 10.0.0.0/16
  - Un sous-réseau avec l’espace d’adressage 10.0.2.0/24
  - Deux adresses IP publiques. Une est utilisée par l’équilibrage de charge du groupe de machines virtuelles identiques et l’autre est utilisée pour la connexion à la jumpbox SSH.

Dans Azure Cloud Shell, effectuez la procédure suivante :

1. Créez un fichier nommé `vmss.tf` pour décrire l’infrastructure du groupe de machines virtuelles identiques.

    ```bash
    code vmss.tf
    ```

1. Collez le code suivant à la fin du fichier pour exposer le nom de domaine complet pour les machines virtuelles.

   ```hcl
   resource "azurerm_resource_group" "vmss" {
    name     = var.resource_group_name
    location = var.location
    tags     = var.tags
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
    location            = var.location
    resource_group_name = azurerm_resource_group.vmss.name
    tags                = var.tags
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
    allocation_method = "Static"
    domain_name_label            = random_string.fqdn.result
    tags                         = var.tags
   }
   ```

1. Enregistrez le fichier ( **&lt;Ctrl>S**) et quittez l’éditeur ( **&lt;Ctrl>Q**).

## <a name="provision-the-network-infrastructure"></a>Approvisionner l’infrastructure réseau
Dans le répertoire où vous avez créé les fichiers de configuration (.tf), utilisez Azure Cloud Shell pour effectuer les étapes suivantes :

1. Initialisez Terraform.

   ```bash
   terraform init
   ```

1. Exécutez la commande suivante pour déployer l’infrastructure définie dans Azure.

   ```bash
   terraform apply
   ```

   Terraform vous invite à entrer une valeur `location`, car la variable `location` est définie dans `variables.tf`, mais n’est jamais renseignée. Vous pouvez entrer n’importe quel emplacement valide, par exemple « USA Ouest », puis sélectionnez Entrée. (Utilisez des parenthèses autour d’une valeur avec des espaces.)

1. Terraform imprime la sortie comme défini dans le fichier `output.tf`. La capture d’écran suivante montre que le nom de domaine complet est au format suivant : `<ID>.<location>.cloudapp.azure.com`. L’ID correspond à une valeur calculée, tandis que l’emplacement est la valeur que vous fournissez lors de l’exécution de Terraform.

   ![Nom de domaine complet du groupe identique de machines virtuelles pour l’adresse IP publique](./media/terraform-create-vm-scaleset-network-disks-hcl/fqdn.png)

1. Dans le menu principal du Portail Azure, sélectionnez **Groupes de ressources**.

1. Dans l’onglet **Groupes de ressources**, sélectionnez **myResourceGroup** pour afficher les ressources qui ont été créées par Terraform.
   ![Ressources réseau du groupe de machines virtuelles identiques](./media/terraform-create-vm-scaleset-network-disks-hcl/resource-group-resources.png)

## <a name="add-a-virtual-machine-scale-set"></a>Ajouter un groupe de machines virtuelles identiques

Dans cette section, vous allez apprendre à ajouter les ressources suivantes au modèle :

- Un équilibreur de charge Azure et des règles pour prendre en charge l’application et l’attacher à l’adresse IP publique configurée précédemment dans cet article
- Un pool d’adresses principales Azure qui est ensuite assigné à l’équilibreur de charge
- Un port de sonde d’intégrité utilisé par l’application et configuré sur l’équilibrage de charge
- Un groupe de machines virtuelles identiques derrière l’équilibreur de charge, exécuté sur le réseau virtuel déployé précédemment dans cet article
- [Nginx](https://nginx.org/) sur les nœuds du groupe de machines virtuelles identiques à l’aide de [cloud-init](https://cloudinit.readthedocs.io/en/latest/).

Dans Cloud Shell, effectuez la procédure suivante :

1. Ouvrez le fichier config `vmss.tf`.

   ```bash
   code vmss.tf
   ```

1. Accédez à la fin du fichier et entrez dans le mode ajout en sélectionnant la touche A.

1. Copiez le code suivant à la fin du fichier :

   ```hcl
   resource "azurerm_lb" "vmss" {
    name                = "vmss-lb"
    location            = var.location
    resource_group_name = azurerm_resource_group.vmss.name

    frontend_ip_configuration {
      name                 = "PublicIPAddress"
      public_ip_address_id = azurerm_public_ip.vmss.id
    }

    tags = var.tags
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
      publisher = "Canonical"
      offer     = "UbuntuServer"
      sku       = "16.04-LTS"
      version   = "latest"
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
      admin_username       = var.admin_user
      admin_password       = var.admin_password
      custom_data          = file("web.conf")
    }

    os_profile_linux_config {
      disable_password_authentication = false
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

    tags = var.tags
   }
   ```

1. Enregistrez le fichier et quittez l’éditeur vi en entrant la commande suivante :

    ```bash
    :wq
    ```

1. Créez un fichier nommé `web.conf` pour prendre en charge la configuration cloud-init des machines virtuelles appartenant au groupe identique.

    ```bash
    code web.conf
    ```

1. Collez le code suivant dans l’éditeur :

   ```hcl
   #cloud-config
   packages:
    - nginx
   ```

1. Enregistrez le fichier et quittez l’éditeur vi en entrant la commande suivante :

     ```bash
     :wq
     ```

1. Ouvrez le fichier config `variables.tf`.

    ```bash
    code variables.tf
    ```

1. Accédez à la fin du fichier et entrez dans le mode ajout en sélectionnant la touche A.

1. Personnalisez le déploiement en collant le code suivant à la fin du fichier :

    ```hcl
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

1. Enregistrez le fichier ( **&lt;Ctrl>S**) et quittez l’éditeur ( **&lt;Ctrl>Q**).

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
Un serveur *jumpbox* SSH est un serveur unique par lequel vous passez pour accéder à d’autres serveurs du réseau. Cette étape vous permet de configurer les ressources suivantes :

- Une interface réseau (ou jumpbox) connectée au même sous-réseau que le groupe de machines virtuelles identiques.

- Une machine virtuelle connectée avec cette interface réseau. Cette « jumpbox » est accessible à distance. Une fois connecté, vous pouvez utiliser SSH pour accéder à n’importe quelle machine virtuelle du groupe de machines virtuelles identiques.

1. Ouvrez le fichier config `vmss.tf`.

   ```bash
   code vmss.tf
   ```

1. Accédez à la fin du fichier et entrez dans le mode ajout en sélectionnant la touche A.

1. Copiez le code suivant à la fin du fichier :

   ```hcl
   resource "azurerm_public_ip" "jumpbox" {
    name                         = "jumpbox-public-ip"
    location                     = var.location
    resource_group_name          = azurerm_resource_group.vmss.name
    allocation_method = "Static"
    domain_name_label            = "${random_string.fqdn.result}-ssh"
    tags                         = var.tags
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

    tags = var.tags
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
      admin_username = var.admin_user
      admin_password = var.admin_password
    }

    os_profile_linux_config {
      disable_password_authentication = false
    }

    tags = var.tags
   }
   ```

1. Ouvrez le fichier config `output.tf`.

   ```bash
   code output.tf
   ```

1. Accédez à la fin du fichier et entrez dans le mode ajout en sélectionnant la touche A.

1. Collez le code suivant à la fin du fichier pour afficher le nom d’hôte de la jumpbox lorsque le déploiement est terminé :

   ```hcl
   output "jumpbox_public_ip" {
      value = azurerm_public_ip.jumpbox.fqdn
   }
   ```

1. Enregistrez le fichier ( **&lt;Ctrl>S**) et quittez l’éditeur ( **&lt;Ctrl>Q**).

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

> [!div class="nextstepaction"] 
> [En savoir plus sur l’utilisation de Terraform dans Azure](/azure/terraform)
