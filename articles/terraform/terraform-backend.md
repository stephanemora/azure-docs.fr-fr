---
title: 'Tutoriel : Stocker l’état de Terraform dans le stockage Azure'
description: Introduction au stockage de l’état Terraform dans le stockage Azure.
ms.topic: tutorial
ms.date: 11/07/2019
ms.openlocfilehash: c94c66969c517bd0e51117ab7c6ed6a889149518
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459013"
---
# <a name="tutorial-store-terraform-state-in-azure-storage"></a>Tutoriel : Stocker l’état de Terraform dans le stockage Azure

L’état Terraform est utilisé pour rapprocher des ressources déployées avec les configurations de Terraform. L’état permet à Terraform de connaître les ressources Azure à ajouter, mettre à jour ou supprimer. Par défaut, l’état Terraform est stocké localement lors de l’exécution de la commande `terraform apply`. Cette configuration n’est pas idéale pour les raisons suivantes :

- L’état local ne fonctionne pas correctement dans une équipe ou un environnement de collaboration.
- L’état Terraform peut inclure des informations sensibles.
- Le stockage de l’état localement augmente les risques de suppression accidentelle.

Terraform prend en charge la persistance de l’état dans le stockage étendu. Le stockage Azure est un back-end pris en charge de ce type. Ce document montre comment configurer et utiliser le stockage Azure à cet effet.

## <a name="configure-storage-account"></a>Configurer le compte de stockage

Avant d’utiliser le stockage Azure comme back-end, vous devez créer un compte de stockage. Le compte de stockage peut être créé avec le portail Azure, PowerShell, l’interface Azure CLI ou Terraform lui-même. L’exemple suivant permet de configurer le compte de stockage avec l’interface Azure CLI.

```azurecli
#!/bin/bash

RESOURCE_GROUP_NAME=tstate
STORAGE_ACCOUNT_NAME=tstate$RANDOM
CONTAINER_NAME=tstate

# Create resource group
az group create --name $RESOURCE_GROUP_NAME --location eastus

# Create storage account
az storage account create --resource-group $RESOURCE_GROUP_NAME --name $STORAGE_ACCOUNT_NAME --sku Standard_LRS --encryption-services blob

# Get storage account key
ACCOUNT_KEY=$(az storage account keys list --resource-group $RESOURCE_GROUP_NAME --account-name $STORAGE_ACCOUNT_NAME --query [0].value -o tsv)

# Create blob container
az storage container create --name $CONTAINER_NAME --account-name $STORAGE_ACCOUNT_NAME --account-key $ACCOUNT_KEY

echo "storage_account_name: $STORAGE_ACCOUNT_NAME"
echo "container_name: $CONTAINER_NAME"
echo "access_key: $ACCOUNT_KEY"
```

Notez le nom du compte de stockage, du conteneur et de la clé d’accès de stockage. Ces valeurs sont nécessaires lors de la configuration de l’état à distance.

## <a name="configure-state-back-end"></a>Configurer un back-end d’état

Le back-end d’état Terraform est configuré lorsque vous exécutez la commande `terraform init`. Les données suivantes sont nécessaires pour configurer le back-end d’état :

- **storage_account_name** : Nom du compte Stockage Azure.
- **container_name** : Nom du conteneur d’objets blob.
- **clé** : Nom du fichier de magasin d’état à créer.
- **access_key** : Clé d’accès de stockage.

Chacune de ces valeurs peut être spécifiée dans le fichier de configuration Terraform ou sur la ligne de commande. Nous vous recommandons d’utiliser une variable d’environnement pour la valeur `access_key`. Utiliser une variable d’environnement empêche la clé d’être écrite dans le disque.

Créez une variable d’environnement nommée `ARM_ACCESS_KEY` avec la valeur de la clé d’accès de stockage Azure.

```bash
export ARM_ACCESS_KEY=<storage access key>
```

Pour mieux protéger la clé d’accès du compte de stockage Azure, stockez-la dans Azure Key Vault. La variable d’environnement peut ensuite être définie à l’aide d’une commande semblable à ce qui suit. Pour plus d’informations sur Azure Key Vault, consultez la [documentation concernant Azure Key Vault](../key-vault/secrets/quick-create-cli.md).

```bash
export ARM_ACCESS_KEY=$(az keyvault secret show --name terraform-backend-key --vault-name myKeyVault --query value -o tsv)
```

Pour configurer Terraform afin d’utiliser le back-end, vous devez effectuer les étapes suivantes :
- Incluez un bloc de configuration `backend` avec le type `azurerm`.
- Ajoutez une valeur `storage_account_name` au bloc de configuration.
- Ajoutez une valeur `container_name` au bloc de configuration.
- Ajoutez une valeur `key` au bloc de configuration.

L’exemple suivant configure un back-end Terraform et crée un groupe de ressources Azure.

```hcl
terraform {
  backend "azurerm" {
    resource_group_name   = "tstate"
    storage_account_name  = "tstate09762"
    container_name        = "tstate"
    key                   = "terraform.tfstate"
  }
}

resource "azurerm_resource_group" "state-demo-secure" {
  name     = "state-demo"
  location = "eastus"
}
```

Initialisez la configuration en procédant comme suit :

1. Exécutez la commande `terraform init`.
1. Exécutez la commande `terraform apply`.

Vous pouvez maintenant trouver le fichier d’état dans Azure Storage Blob.

## <a name="state-locking"></a>Verrouillage de l’état

Les objets blob Azure Storage Blob sont automatiquement verrouillés avant toute opération d’écriture de l’état. Ce modèle empêche les opérations d’état simultanées, lesquelles peuvent entraîner un endommagement. 

Pour plus d’informations, consultez [Verrouillage de l’état](https://www.terraform.io/docs/state/locking.html) dans la documentation Terraform.

Vous pouvez voir le verrou quand vous examinez l’objet blob via le portail Azure ou d’autres outils de gestion Azure.

![Blob Azure avec verrou](media/terraform-backend/lock.png)

## <a name="encryption-at-rest"></a>Chiffrement au repos

Les données stockées dans un objet blob Azure sont chiffrées avant d’être conservées. Si nécessaire, Terraform récupère l’état du back-end et le stocke dans la mémoire locale. À l’aide de ce modèle, l’état n’est jamais écrit sur votre disque local.

Pour plus d’informations sur le chiffrement du stockage Azure, consultez [Chiffrement du service de stockage Azure pour les données au repos](../storage/common/storage-service-encryption.md).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"] 
> [En savoir plus sur l’utilisation de Terraform dans Azure](/azure/terraform)
