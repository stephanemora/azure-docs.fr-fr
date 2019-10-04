---
title: Distribuer du contenu issu du Stockage Azure sur Linux - App Service
description: Guide pratique pour configurer et distribuer du contenu issu du Stockage Azure dans Azure App Service sur Linux.
author: msangapu
manager: jeconnoc
ms.service: app-service
ms.workload: web
ms.topic: article
ms.date: 2/04/2019
ms.author: msangapu
ms.openlocfilehash: 97c03ad294bba1f8a0285fff4595991ca0acc8b5
ms.sourcegitcommit: 71db032bd5680c9287a7867b923bf6471ba8f6be
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/16/2019
ms.locfileid: "71018274"
---
# <a name="serve-content-from-azure-storage-in-app-service-on-linux"></a>Distribuer du contenu issu du Stockage Azure dans App Service sur Linux

Ce guide montre comment distribuer du contenu statique dans App Service sur Linux avec le [Stockage Azure](/azure/storage/common/storage-introduction). Les avantages sont multiples : sécurisation et portabilité du contenu, stockage persistant, accès à plusieurs applications et méthodes de transfert multiples.

## <a name="prerequisites"></a>Prérequis

- une application web existante (App Service sur Linux ou Web App pour conteneurs) ;
- [Azure CLI](/cli/azure/install-azure-cli) 2.0.46 (ou version ultérieure).

## <a name="create-azure-storage"></a>Créer un compte de Stockage Azure

> [!NOTE]
> Le Stockage Azure, facturé séparément et non fourni avec l’application web, n’est pas le stockage par défaut.
>
> La fonctionnalité Bring Your Own Storage ne prend pas en charge l’utilisation de la configuration du pare-feu de stockage en raison de restrictions liées à l’infrastructure.
>

Créez un [compte de stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-cli).

```azurecli
#Create Storage Account
az storage account create --name <storage_account_name> --resource-group myResourceGroup

#Create Storage Container
az storage container create --name <storage_container_name> --account-name <storage_account_name>
```

## <a name="upload-files-to-azure-storage"></a>Charger des fichiers dans le Stockage Azure

Pour charger un répertoire local dans le compte de stockage, utilisez la commande [`az storage blob upload-batch`](https://docs.microsoft.com/cli/azure/storage/blob?view=azure-cli-latest#az-storage-blob-upload-batch), comme dans l’exemple suivant :

```azurecli
az storage blob upload-batch -d <full_path_to_local_directory> --account-name <storage_account_name> --account-key "<access_key>" -s <source_location_name>
```

## <a name="link-storage-to-your-web-app-preview"></a>Lier le stockage à une application web (préversion)

> [!CAUTION]
> Le fait de lier un répertoire existant d’une application web à un compte de stockage a pour effet de supprimer le contenu du répertoire. Si vous migrez des fichiers pour une application existante, faites une sauvegarde de votre application et de son contenu avant de commencer.
>

Pour monter un compte de stockage dans un répertoire de votre application App Service, utilisez la commande [`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add). Le type de stockage peut être AzureBlob ou AzureFiles. Pour ce conteneur, on utilisera AzureBlob.

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureBlob --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory>
```

Répétez cette opération pour tous les autres annuaires que vous souhaitez lier à un compte de stockage.

## <a name="verify"></a>Vérifier

Vous pouvez vérifier qu’un conteneur de stockage est lié à une application web en exécutant la commande suivante :

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```

## <a name="use-custom-storage-in-docker-compose"></a>Utiliser le stockage personnalisé dans Docker Compose

Stockage Azure peut être monté avec des applications à plusieurs conteneurs à l’aide de l’id personnalisé. Pour afficher le nom de l’id personnalisé, exécutez [`az webapp config storage-account list --name <app_name> --resource-group <resource_group>`](/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-list).

Dans votre fichier *docker-compose.yml*, mappez l’option `volumes` sur `custom-id`. Par exemple :

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - <custom-id>:<path_in_container>
```

## <a name="next-steps"></a>Étapes suivantes

- [Configurer des applications web dans Azure App Service](../configure-common.md)
