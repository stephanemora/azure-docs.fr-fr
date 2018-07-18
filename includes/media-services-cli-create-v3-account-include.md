---
title: Fichier Include
description: Fichier Include
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 04/13/2018
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: 9ecb07a2cb278f6cde4ffdc3b252cb9e816d08da
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38733290"
---
## <a name="create-a-media-services-account"></a>Créer un compte Media Services

Vous devez d’abord créer un compte Media Services. Cette section montre ce dont vous avez besoin pour la création d’un compte à l’aide de Azure CLI.

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources à l’aide de la commande suivante. Un groupe de ressources Azure est un conteneur logique dans lequel les ressources comme les comptes Azure Media Services et les comptes de stockage associés sont déployées et gérées.

```azurecli-interactive
az group create --name amsResourceGroup --location westus2
```

### <a name="create-a-storage-account"></a>Créez un compte de stockage.

Lorsque vous créez un compte Media Services, vous devez indiquer le nom d’une ressource de compte de stockage Azure. Le compte de stockage spécifié est lié à votre compte Media Services. 

Vous devez avoir un compte de stockage **principal** et vous pouvez avoir n’importe quel nombre de comptes de stockage **secondaires** associés à votre compte Media Services. Media Services prend en charge les comptes **v2 à usage général** (GPv2) ou **v1 à usage général** (GPv1). Les comptes Blob uniquement ne sont pas autorisés en tant que comptes **principaux**. Pour plus d’informations sur les comptes de stockage, consultez [Options du compte de stockage Azure](../articles/storage/common/storage-account-options.md). 

Pour plus d’informations sur l’utilisation des comptes de stockage dans Media Services, consultez [Comptes de stockage](../articles/media-services/latest/storage-account-concept.md).

La commande suivante crée un compte de stockage qui sera associé au compte Media Services. Dans le script ci-dessous, vous pouvez remplacer `storageaccountforams` par votre valeur. La longueur du nom du compte doit être inférieure à 24.

```azurecli-interactive
az storage account create --name storageaccountforams \  
--kind StorageV2 \
--sku Standard_RAGRS \
--resource-group amsResourceGroup
```

### <a name="create-a-media-services-account"></a>Créer un compte Media Services

La commande suivante de Azure CLI crée un nouveau compte Media Services. Vous pouvez remplacer les valeurs suivantes : `amsaccount` `storageaccountforams` (doit correspondre à la valeur donnée pour votre compte de stockage), et `amsResourceGroup` (doit correspondre à la valeur donnée pour le groupe de ressources).

```azurecli-interactive
az ams account create --name amsaccount --resource-group amsResourceGroup --storage-account storageaccountforams
```
