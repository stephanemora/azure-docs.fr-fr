---
title: Distribuer du contenu issu du Stockage Azure à des conteneurs Linux
description: Découvrez comment attacher un partage réseau personnalisé à votre conteneur Linux dans Azure App Service. Partagez des fichiers entre plusieurs applications, gérez le contenu statique à distance, accédez localement, etc.
author: msangapu-msft
ms.topic: article
ms.date: 01/02/2020
ms.author: msangapu
ms.openlocfilehash: 9a5a38ea32d927f50fb9ddbebe3e1c3533e6fcc0
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82625321"
---
# <a name="serve-content-from-azure-storage-in-app-service-on-linux"></a>Distribuer du contenu issu du Stockage Azure dans App Service sur Linux

> [!NOTE]
> Cet article s’applique aux conteneurs Linux. Pour effectuer un déploiement sur des conteneurs Windows personnalisés, consultez [Configurer Azure Files dans un conteneur Windows sur App Service](../configure-connect-to-azure-storage.md). Stockage Azure dans App Service sous Linux est une fonctionnalité en **préversion**. Cette fonctionnalité **n’est pas prise en charge pour les scénarios de production**.
>

Ce guide montre comment joindre Stockage Azure à App Service sur Linux. Les avantages sont multiples : sécurisation et portabilité du contenu, stockage persistant, accès à plusieurs applications et méthodes de transfert multiples.

## <a name="prerequisites"></a>Prérequis

- [Azure CLI](/cli/azure/install-azure-cli) 2.0.46 (ou version ultérieure).
- Une [application Azure App Service sur Linux](https://docs.microsoft.com/azure/app-service/containers/).
- [Compte Stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-cli)
- Un [partage et répertoire de fichiers Azure](../../storage/files/storage-how-to-use-files-cli.md).


## <a name="limitations-of-azure-storage-with-app-service"></a>Limitations de Stockage Azure avec App Service

- Stockage Azure avec App Service est en **préversion** pour App Service sur Linux et Web App pour conteneurs. Elle **n’est pas prise en charge** pour les **scénarios de production**.
- Stockage Azure avec App Service prend en charge le montage de **conteneurs Azure Files** (lecture/écriture) et de **conteneurs d’objets blob Azure** (lecture seule)
- Stockage Azure avec App Service **ne prend pas en charge** l’utilisation de la configuration **Storage Firewall** en raison des restrictions liées à l’infrastructure.
- Stockage Azure avec App Service vous permet de spécifier **jusqu’à cinq** points de montage par application.
- Le stockage Azure monté sur une application n’est pas accessible via des points de terminaison FTP/FTPS App Service. Utilisez [Explorateur Stockage Azure](https://azure.microsoft.com/features/storage-explorer/).
- Stockage Azure **n’est pas inclus** avec votre application web et est facturé séparément. Apprenez-en davantage sur la [tarification Stockage Azure](https://azure.microsoft.com/pricing/details/storage).

> [!WARNING]
> Les configurations App Service utilisant Stockage Blob Azure passeront en lecture seule en février 2020. [En savoir plus](https://github.com/Azure/app-service-linux-docs/blob/master/BringYourOwnStorage/mounting_azure_blob.md)
>

## <a name="configure-your-app-with-azure-storage"></a>Configurer votre application avec Stockage Azure

Une fois que vous avez créé [votre compte Stockage Azure, le partage de fichiers et l’annuaire](#prerequisites), vous pouvez configurer votre application avec Stockage Azure.

Pour monter un compte de stockage dans un répertoire de votre application App Service, utilisez la commande [`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add). Le type de stockage peut être AzureBlob ou AzureFiles. AzureFiles est utilisé dans cet exemple. Le paramètre de chemin d’accès de montage correspond au dossier que vous souhaitez monter à partir de Stockage Azure. Le définir sur « / » a pour effet de monter entièrement Stockage Azure.


> [!CAUTION]
> Le répertoire spécifié comme chemin de montage dans votre application web doit être vide. Tout contenu stocké dans ce répertoire sera supprimé lors de l’ajout d’un montage externe. Si vous migrez des fichiers pour une application existante, faites une sauvegarde de votre application et de son contenu avant de commencer.
>

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory>
```

Répétez cette opération pour tous les autres annuaires que vous souhaitez lier à un compte de stockage.

## <a name="verify-azure-storage-link-to-the-web-app"></a>Vérifier le lien Stockage Azure vers l’application web

Vous pouvez vérifier qu’un conteneur de stockage est lié à une application web en exécutant la commande suivante :

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```

## <a name="use-azure-storage-in-docker-compose"></a>Utiliser Stockage Azure dans Docker Compose

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

