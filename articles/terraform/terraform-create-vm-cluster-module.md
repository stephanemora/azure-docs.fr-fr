---
title: Tutoriel - Créer un cluster de machines virtuelles Azure avec Terraform à l’aide du Registre des modules
description: Dans ce tutoriel, vous allez voir comment utiliser les modules Terraform pour créer un cluster de machines virtuelles Windows dans Azure.
keywords: azure devops terraform machine virtuelle cluster module registre
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: 9faeee9bb2f0fb6dc148a3868f6fc0dae3833a2a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "78945292"
---
# <a name="tutorial-create-an-azure-vm-cluster-with-terraform-using-the-module-registry"></a>Tutoriel : Créer un cluster de machines virtuelles Azure avec Terraform à l’aide du Registre des modules

Cet article vous guide dans la création d’un petit cluster de machines virtuelles avec le [module de calcul Azure](https://registry.terraform.io/modules/Azure/compute/azurerm/1.0.2) de Terraform. Ce didacticiel vous montre comment effectuer les opérations suivantes : 

> [!div class="checklist"]
> * Configurer l’authentification avec Azure
> * Créer le modèle Terraform
> * Visualiser les modifications avec un plan
> * Appliquer la configuration pour créer le cluster de machines virtuelles

Pour plus d’informations sur Terraform, consultez la [documentation Terraform](https://www.terraform.io/docs/index.html).

## <a name="set-up-authentication-with-azure"></a>Configurer l’authentification avec Azure

> [!TIP]
> Si vous [utilisez des variables d’environnement Terraform](terraform-install-configure.md) ou exécutez ce didacticiel dans [Azure Cloud Shell](/azure/cloud-shell/overview), ignorez cette étape.

 Consultez [Installer Terraform et configurer l’accès à Azure](terraform-install-configure.md) pour créer un principal du service Azure. Utilisez ce principal du service pour renseigner un nouveau fichier `azureProviderAndCreds.tf` dans un répertoire vide avec le code suivant :

```hcl
variable subscription_id {}
variable tenant_id {}
variable client_id {}
variable client_secret {}

provider "azurerm" {
    version = "~>1.40"

    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}
```

## <a name="create-the-template"></a>Créer le modèle

Créez un modèle Terraform nommé `main.tf` avec le code suivant :

```hcl
module mycompute {
    source = "Azure/compute/azurerm"
    resource_group_name = "myResourceGroup"
    location = "East US 2"
    admin_password = "ComplxP@assw0rd!"
    vm_os_simple = "WindowsServer"
    is_windows_image = "true"
    remote_port = "3389"
    nb_instances = 2
    public_ip_dns = ["unique_dns_name"]
    vnet_subnet_id = module.network.vnet_subnets[0]
}

module "network" {
    source = "Azure/network/azurerm"
    location = "East US 2"
    resource_group_name = "myResourceGroup"
}

output "vm_public_name" {
    value = module.mycompute.public_ip_dns_name
}

output "vm_public_ip" {
    value = module.mycompute.public_ip_address
}

output "vm_private_ips" {
    value = module.mycompute.network_interface_private_ip
}
```

Exécutez `terraform init` dans votre répertoire de configuration. Avec la version 0.10.6 de Terraform ou une version supérieure, vous obtenez la sortie suivante :

![Terraform Init](media/terraformInitWithModules.png)

## <a name="visualize-the-changes-with-plan"></a>Visualiser les modifications avec un plan

Exécutez `terraform plan` pour obtenir un aperçu de l’infrastructure de machines virtuelles créée par le modèle.

![Terraform Plan](media/terraform-create-vm-cluster-with-infrastructure/terraform-plan.png)


## <a name="create-the-virtual-machines-with-apply"></a>Créer les machines virtuelles avec apply

Exécutez `terraform apply` pour approvisionner les machines virtuelles sur Azure.

![Terraform Apply](media/terraform-create-vm-cluster-with-infrastructure/terraform-apply.png)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"] 
> [Parcourir la liste des modules Azure Terraform](https://registry.terraform.io/modules/Azure)