---
title: Utilisation de Terraform avec Azure Cloud Shell
description: Utilisez Terraform avec Azure Cloud Shell pour simplifier l’authentification et la configuration des modèles.
services: terraform
ms.service: azure
keywords: terraform, devops, groupe de machines virtuelles identiques, machine virtuelle, réseau, stockage, modules
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 10/19/2017
ms.openlocfilehash: ab2fd0c7fa546201d6eb19f727053a9ac54fa854
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "66169926"
---
# <a name="terraform-cloud-shell-development"></a>Développement de Terraform Cloud Shell 

Terraform fonctionne parfaitement à partir d’une ligne de commande Bash telle que macOS Terminal ou un interpréteur de commandes sous Windows ou Linux. Exécuter vos configurations Terraform dans le cadre de l’expérience Bash [d’Azure Cloud Shell](/azure/cloud-shell/overview) permet d’accélérer votre cycle de développement.

Cet article sur les concepts présente les fonctionnalités de Cloud Shell qui vous permettent d’écrire des scripts Terraform en vue d’un déploiement sur Azure.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Configuration automatique des informations d’identification

Terraform est installé et disponible immédiatement dans Cloud Shell. Les scripts Terraform s’authentifient avec Azure lorsque vous vous connectez à Cloud Shell pour gérer l’infrastructure sans aucune configuration supplémentaire. Grâce à l’authentification automatique, il n’est pas nécessaire de créer manuellement un principal de service Active Directory et de configurer les variables du fournisseur Azure Terraform.


## <a name="using-modules-and-providers"></a>Utilisation des modules et des fournisseurs

Les modules Azure Terraform nécessitent des informations d’identification pour accéder aux ressources de votre abonnement Azure et les modifier. Lorsque vous travaillez dans Cloud Shell, ajoutez le code suivant à vos scripts pour utiliser les modules Azure Terraform dans Cloud Shell :

```tf
# Configure the Microsoft Azure Provider
provider "azurerm" {
}
```

Le Cloud Shell transmet les valeurs requises pour le fournisseur `azurerm` via des variables d’environnement lors de l’utilisation de commandes d’interface de ligne de commande `terraform`.

## <a name="other-cloud-shell-developer-tools"></a>Autres outils de développement Cloud Shell

Les fichiers et les états de l’interpréteur de commandes sont conservés dans le stockage Azure d’une session Cloud Shell à l’autre. Utilisez [l’Explorateur Stockage Azure](/azure/vs-azure-tools-storage-manage-with-storage-explorer) pour copier et charger des fichiers dans Cloud Shell à partir de votre ordinateur local.

L’interface de ligne de commande Azure est disponible dans le Cloud Shell et constitue un outil idéal pour tester les configurations et vérifier votre travail après l’exécution d’une opération `terraform apply` ou `terraform destroy`.


## <a name="next-steps"></a>Étapes suivantes

[Créer un petit cluster de machines virtuelles à l’aide du registre de modules](terraform-create-vm-cluster-module.md)
[Créer un petit cluster de machines virtuelles à l’aide du langage HCL personnalisé](terraform-create-vm-cluster-with-infrastructure.md)
