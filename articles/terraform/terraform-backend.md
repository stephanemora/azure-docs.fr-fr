---
title: Utiliser le stockage Azure comme serveur principal Terraform
description: Introduction au stockage de l’état Terraform dans le stockage Azure.
services: terraform
author: tomarchermsft
ms.service: azure
ms.topic: article
ms.date: 09/13/2018
ms.author: tarcher
ms.openlocfilehash: a88ad25e335026d5172c7997f62629d5ada46f6e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66693299"
---
# <a name="store-terraform-state-in-azure-storage"></a>Stocker l’état de Terraform dans le stockage Azure

L’état Terraform est utilisé pour rapprocher des ressources déployées avec les configurations de Terraform. En utilisant l’état, Terraform connaît les ressources Azure à ajouter, mettre à jour ou supprimer. Par défaut, l’état Terraform est stocké localement lors de l’exécution de *Terraform apply*. Cette configuration n’est pas idéale pour plusieurs raisons :

- L’état local ne fonctionne pas correctement dans une équipe ou un environnement de collaboration
- L’état Terraform peut inclure des informations sensibles
- Stocker l’état localement augmente les risques de suppression accidentelle

Terraform inclut le concept de serveur principal d’état, qui est un stockage à distance pour l’état Terraform. Lorsque vous utilisez un serveur principal d’état, le fichier d’état est stocké dans un magasin de données tel que le stockage Azure. Ce document décrit en détail comment configurer et utiliser le stockage Azure comme serveur principal d’état Terraform.

## <a name="configure-storage-account"></a>Configurer le compte de stockage

Avant d’utiliser le stockage Azure comme serveur principal, un compte de stockage doit être créé. Le compte de stockage peut être créé avec le portail Azure, PowerShell, l’interface Azure CLI ou Terraform lui-même. L’exemple suivant permet de configurer le compte de stockage avec l’interface Azure CLI.

```azurecli-interactive
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

## <a name="configure-state-backend"></a>Configurer le serveur principal d’état

Le serveur principal d’état Terraform est configuré lors de l’exécution de *Terraform init*. Pour configurer le serveur principal d’état, les données suivantes sont requises.

- storage_account_name : le nom de votre compte de stockage Azure.
- container_name : le nom du conteneur d’objets blob.
- key : le nom du fichier de magasin d’état qui doit être créé.
- access_key : la clé d’accès de stockage.

Chacune de ces valeurs peut être spécifiée dans le fichier de configuration Terraform ou sur la ligne de commande. Toutefois, il est recommandé d’utiliser une variable d’environnement pour la `access_key`. Utiliser une variable d’environnement empêche la clé d’être écrite dans le disque.

Créez une variable d’environnement nommée `ARM_ACCESS_KEY` avec la valeur de la clé d’accès de stockage Azure.

```console
export ARM_ACCESS_KEY=<storage access key>
```

Pour mieux protéger la clé d’accès du compte de stockage Azure, stockez-la dans Azure Key Vault. La variable d’environnement peut ensuite être définie à l’aide d’une commande semblable à ce qui suit. Pour plus d’informations sur Azure Key Vault, consultez la [documentation concernant Azure Key Vault][azure-key-vault].

```console
export ARM_ACCESS_KEY=$(az keyvault secret show --name terraform-backend-key --vault-name myKeyVault --query value -o tsv)
```

Pour configurer Terraform afin d’utiliser le serveur principal, vous devez inclure une configuration de *serveur principal* avec un type d’*azurerm* à l’intérieur de la configuration de Terraform. Ajoutez les valeurs *storage_account_name*, *container_name*, et *key* au bloc de configuration.

L’exemple suivant configure un back-end Terraform et crée un groupe de ressources Azure. Remplacez les valeurs avec les valeurs de votre environnement.

```json
terraform {
  backend "azurerm" {
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

À présent, initialisez la configuration avec *Terraform init*, puis exécutez la configuration avec *Terraform apply*. Une fois cela terminé, vous trouverez le fichier d’état dans le blob Azure Storage Blob.

## <a name="state-locking"></a>Verrouillage de l’état

Lorsque vous utilisez un blob Azure Storage Blob pour le stockage de l’état, l’objet blob est automatiquement verrouillé avant toute opération d’écriture de l’état. Cette configuration empêche plusieurs opérations d’état simultanées, lesquelles peuvent entraîner une corruption. Pour plus d’informations, consultez le [Verrouillage d’état][terraform-state-lock] dans la documentation Terraform.

Le verrou peut être vu lorsque vous examinez l’objet blob via le portail Azure ou d’autres outils de gestion Azure.

![Blob Azure avec verrou](media/terraform-backend/lock.png)

## <a name="encryption-at-rest"></a>Chiffrement au repos

Par défaut, les données stockées dans un blob Azure sont chiffrées avant d’être conservées dans l’infrastructure de stockage. Lorsque Terraform a besoin d’un état, il est récupéré depuis le serveur principal et stocké en mémoire sur votre système de développement. Dans cette configuration, l’état est sécurisé dans le stockage Azure et n’est pas écrit sur votre disque local.

Pour plus d’informations sur le chiffrement du stockage Azure, consultez [Azure Storage Service Encryption pour les données au repos][azure-storage-encryption].

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la configuration du back-end Terraform, consultez la [documentation du back-end Terraform][terraform-backend].

<!-- LINKS - internal -->
[azure-key-vault]: ../key-vault/quick-create-cli.md
[azure-storage-encryption]: ../storage/common/storage-service-encryption.md

<!-- LINKS - external -->
[terraform-azurerm]: https://www.terraform.io/docs/backends/types/azurerm.html
[terraform-backend]: https://www.terraform.io/docs/backends/
[terraform-state-lock]: https://www.terraform.io/docs/state/locking.html
