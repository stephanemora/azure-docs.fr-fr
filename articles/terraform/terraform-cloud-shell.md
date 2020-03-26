---
title: 'Tutoriel : Configurer Azure Cloud Shell pour Terraform'
description: Dans ce tutoriel, vous utilisez Terraform avec Azure Cloud Shell pour simplifier l’authentification et la configuration des modèles.
keywords: azure devops terraform cloud shell
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: 3a9db1143ba07b549a271d53d610e0a4853467c6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "78945328"
---
# <a name="tutorial-configure-azure-cloud-shell-for-terraform"></a>Tutoriel : Configurer Azure Cloud Shell pour Terraform

Terraform fonctionne bien à partir d’une ligne de commande Bash dans macOS, Windows ou Linux. L’exécution de vos configurations Terraform dans le cadre de l’expérience Bash d’[Azure Cloud Shell](/azure/cloud-shell/overview) présente quelques avantages uniques. Ce tutoriel montre comment écrire des scripts Terraform qui sont déployés sur Azure à l’aide de Cloud Shell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Configuration automatique des informations d’identification

Terraform est installé et disponible immédiatement dans Cloud Shell. Les scripts Terraform s’authentifient avec Azure lorsque vous vous connectez à Cloud Shell pour gérer l’infrastructure sans aucune configuration supplémentaire. L’authentification automatique contourne deux processus manuels :
- Création d’un principal de service Active Directory
- Configuration des variables du fournisseur Azure Terraform


## <a name="use-modules-and-providers"></a>Utiliser des modules et des fournisseurs

Les modules Azure Terraform requièrent des informations d’identification pour accéder à des ressources Azure et les modifier. Pour utiliser les modules Terraform dans Cloud Shell, ajoutez le code suivant :


```hcl
# Configure the Microsoft Azure Provider
provider "azurerm" {
    # The "feature" block is required for AzureRM provider 2.x. 
    # If you are using version 1.x, the "features" block is not allowed.
    version = "~>2.0"
    features {}
}
```

Cloud Shell transmet les valeurs requises pour le fournisseur `azurerm` via des variables d’environnement lors de l’utilisation de commandes d’interface de ligne de commande `terraform`.

## <a name="other-cloud-shell-developer-tools"></a>Autres outils de développement Cloud Shell

Les fichiers et les états de l’interpréteur de commandes sont conservés dans le stockage Azure d’une session Cloud Shell à l’autre. Utilisez l’[Explorateur Stockage Azure](/azure/vs-azure-tools-storage-manage-with-storage-explorer) pour copier et charger des fichiers dans Cloud Shell à partir de votre ordinateur local.

L’interface de ligne de commande Azure est disponible dans Cloud Shell et constitue un outil idéal pour tester les configurations et vérifier votre travail après l’exécution d’une opération `terraform apply` ou `terraform destroy`.


## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer un petit cluster de machines virtuelles à l’aide du Registre du module](terraform-create-vm-cluster-module.md)