---
title: Fichier Include
description: Fichier Include
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 05/01/2019
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: feec6a695ad867d26d32904d020648b029f9da35
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177153"
---
## <a name="create-a-media-services-account"></a>Créer un compte Media Services

Vous devez d’abord créer un compte Media Services. Cette section montre ce dont vous avez besoin pour la création d’un compte à l’aide d’Azure CLI.

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources à l’aide de la commande suivante. Un groupe de ressources Azure est un conteneur logique dans lequel les ressources comme les comptes Azure Media Services et les comptes de stockage associés sont déployées et gérées.

Vous pouvez remplacer `amsResourceGroup` par votre valeur.

```azurecli
az group create --name amsResourceGroup --location westus2
```

### <a name="create-a-storage-account"></a>Créez un compte de stockage.

Lorsque vous créez un compte Media Services, vous devez indiquer le nom d’une ressource de compte de stockage Azure. Le compte de stockage spécifié est lié à votre compte Media Services. Pour plus d’informations sur l’utilisation des comptes de stockage dans Media Services, consultez [Comptes de stockage](../articles/media-services/latest/storage-account-concept.md).

Vous devez avoir un compte de stockage **principal** et vous pouvez avoir n’importe quel nombre de comptes de stockage **secondaires** associés à votre compte Media Services. Media Services prend en charge les comptes **v2 à usage général** (GPv2) ou **v1 à usage général** (GPv1). Les comptes Blob uniquement ne sont pas autorisés en tant que comptes **principaux**. Pour plus d’informations sur les comptes de stockage, consultez [Options du compte de stockage Azure](../articles/storage/common/storage-account-options.md). 

Dans cet exemple, nous créons un compte v2 universel, LRS standard. Si vous voulez faire des expériences avec des comptes de stockage, utilisez `--sku Standard_LRS`. Cependant, lors de la sélection d’une référence SKU pour la production, envisagez `--sku Standard_RAGRS`, qui offre la réplication géographique pour la continuité de l’activité. Pour plus d’informations, consultez [Comptes de stockage](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest).
 
La commande suivante crée un compte de stockage qui sera associé au compte Media Services. Dans le script ci-dessous, vous pouvez remplacer `storageaccountforams` par votre valeur. `amsResourceGroup` doit correspondre à la valeur que vous avez donnée au groupe de ressources à l’étape précédente. La longueur du nom du compte de stockage doit être inférieure à 24.

```azurecli
az storage account create --name storageaccountforams \  
  --kind StorageV2 \
  --sku Standard_LRS \
  -l westus2 \
  -g amsResourceGroup
```

### <a name="create-a-media-services-account"></a>Créer un compte Media Services

La commande suivante de Azure CLI crée un nouveau compte Media Services. Vous pouvez remplacer les valeurs suivantes : `amsaccount` `storageaccountforams` (doit correspondre à la valeur donnée pour votre compte de stockage), et `amsResourceGroup` (doit correspondre à la valeur donnée pour le groupe de ressources).

```azurecli
az ams account create --name amsaccount \
  -l westus2 \
  -g amsResourceGroup --storage-account storageaccountforams
```
