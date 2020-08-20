---
title: Ajouter un Stockage Azure (conteneur)
description: Découvrez comment attacher un partage réseau personnalisé dans une application conteneurisée dans Azure App Service. Partagez des fichiers entre plusieurs applications, gérez le contenu statique à distance, accédez localement, etc.
author: msangapu-msft
ms.topic: article
ms.date: 7/01/2019
ms.author: msangapu
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 8ced35f30966a96061792ad2171afe19599ed22c
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88077252"
---
# <a name="access-azure-storage-as-a-network-share-from-a-container-in-app-service"></a>Accéder à Stockage Azure en tant que partage réseau à partir d’un conteneur dans App Service

::: zone pivot="container-windows"

Ce guide montre comment attacher des fichiers Stockage Azure en tant que partage réseau à un conteneur Windows dans App Service. Seuls les [partages de fichiers Azure](../storage/files/storage-how-to-use-files-cli.md) et [partages de fichiers Premium](../storage/files/storage-how-to-create-premium-fileshare.md) sont pris en charge. Les avantages sont multiples : sécurisation et portabilité du contenu, accès à plusieurs applications et multiplication des méthodes de transfert.

::: zone-end

::: zone pivot="container-linux"

Ce guide montre comment attacher le Stockage Azure à un conteneur Linux dans App Service. Les avantages sont multiples : sécurisation et portabilité du contenu, stockage persistant, accès à plusieurs applications et méthodes de transfert multiples.

::: zone-end

## <a name="prerequisites"></a>Prérequis

::: zone pivot="container-windows"

- [Une application de conteneur Windows existante dans Azure App Service](quickstart-custom-container.md)
- [Création d’un partage de fichiers Azure](../storage/files/storage-how-to-use-files-cli.md)
- [Charger des fichiers dans un partage de fichiers Azure](../storage/files/storage-files-deployment-guide.md)

::: zone-end

::: zone pivot="container-linux"

- Une [application Azure App Service sur Linux](index.yml).
- [Compte Stockage Azure](../storage/common/storage-account-create.md?tabs=azure-cli)
- Un [partage et répertoire de fichiers Azure](../storage/files/storage-how-to-use-files-cli.md).

::: zone-end

> [!NOTE]
> Azure Files, facturé séparément et non fourni avec l’application web, n’est pas le stockage par défaut. Il ne prend pas en charge la configuration du pare-feu en raison des limitations de l’infrastructure.
>

## <a name="limitations"></a>Limites

::: zone pivot="container-windows"

- Stockage Azure dans App Service est **en préversion** et **non pris en charge** pour les **scénarios de production**.
- Stockage Azure dans App Service n’est actuellement **pas pris en charge** pour les scénarios de type Apportez votre propre code (applications Windows non conteneurisées).
- Stockage Azure dans App Service **ne prend pas en charge** l’utilisation de la configuration **Storage Firewall** en raison des restrictions liées à l’infrastructure.
- Stockage Azure avec App Service vous permet de spécifier **jusqu’à cinq** points de montage par application.
- Le stockage Azure monté sur une application n’est pas accessible via des points de terminaison FTP/FTPS App Service. Utilisez [Explorateur Stockage Azure](https://azure.microsoft.com/features/storage-explorer/).

::: zone-end

::: zone pivot="container-linux"

- Stockage Azure dans App Service est en **préversion** pour App Service sur Linux et Web App pour conteneurs. Elle **n’est pas prise en charge** pour les **scénarios de production**.
- Stockage Azure dans App Service prend en charge le montage de **conteneurs Azure Files** (lecture/écriture) et de **conteneurs blob Azure** (lecture seule)
- Stockage Azure dans App Service **ne prend pas en charge** l’utilisation de la configuration **Storage Firewall** en raison des restrictions liées à l’infrastructure.
- Stockage Azure dans App Service vous permet de spécifier **jusqu’à cinq** points de montage par application.
- Le stockage Azure monté sur une application n’est pas accessible via des points de terminaison FTP/FTPS App Service. Utilisez [Explorateur Stockage Azure](https://azure.microsoft.com/features/storage-explorer/).

::: zone-end

## <a name="link-storage-to-your-app"></a>Lier le stockage à votre application

::: zone pivot="container-windows"

Une fois que vous avez créé [votre compte Stockage Azure, le partage de fichiers et l’annuaire](#prerequisites), vous pouvez configurer votre application avec Stockage Azure.

Pour monter un partage Azure Files dans un répertoire de votre application App Service, utilisez la commande [`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add). Le type de stockage doit être AzureFiles.

```azurecli
az webapp config storage-account add --resource-group <group-name> --name <app-name> --custom-id <custom-id> --storage-type AzureFiles --share-name <share-name> --account-name <storage-account-name> --access-key "<access-key>" --mount-path <mount-path-directory of form c:<directory name> >
```

Répétez cette opération pour tous les autres annuaires que vous souhaitez lier à un partage Azure Files.

::: zone-end

::: zone pivot="container-linux"

Une fois que vous avez créé [votre compte Stockage Azure, le partage de fichiers et l’annuaire](#prerequisites), vous pouvez configurer votre application avec Stockage Azure.

Pour monter un compte de stockage dans un répertoire de votre application App Service, utilisez la commande [`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add). Le type de stockage peut être AzureBlob ou AzureFiles. AzureFiles est utilisé dans cet exemple. Le paramètre de chemin d’accès de montage correspond au dossier que vous souhaitez monter à partir de Stockage Azure. Le définir sur « / » a pour effet de monter entièrement Stockage Azure.


> [!CAUTION]
> Le répertoire spécifié comme chemin de montage dans votre application web doit être vide. Tout contenu stocké dans ce répertoire sera supprimé lors de l’ajout d’un montage externe. Si vous migrez des fichiers pour une application existante, faites une sauvegarde de votre application et de son contenu avant de commencer.
>

```azurecli
az webapp config storage-account add --resource-group <group-name> --name <app-name> --custom-id <custom-id> --storage-type AzureFiles --share-name <share-name> --account-name <storage-account-name> --access-key "<access-key>" --mount-path <mount-path-directory>
```

Répétez cette opération pour tous les autres annuaires que vous souhaitez lier à un compte de stockage.

::: zone-end

## <a name="verify-linked-storage"></a>Vérifier le stockage lié

Une fois le partage lié à l’application, vous pouvez le vérifier en exécutant la commande suivante :

```azurecli
az webapp config storage-account list --resource-group <resource-group> --name <app-name>
```

## <a name="next-steps"></a>Étapes suivantes

::: zone pivot="container-windows"

- [Migrer des logiciels personnalisés vers Azure App Service à l’aide d’un conteneur personnalisé](tutorial-custom-container.md?pivots=container-windows).

::: zone-end

::: zone pivot="container-linux"

- [Configurer un conteneur personnalisé](configure-custom-container.md?pivots=platform-linux).

::: zone-end