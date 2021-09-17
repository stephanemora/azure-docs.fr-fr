---
title: Partager des images des galeries entre locataires
description: Découvrez comment créer des groupes identiques à l’aide d’images partagées entre les locataires Azure avec Shared Image Gallery.
author: cynthn
ms.author: cynthn
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: shared-image-gallery
ms.date: 04/05/2019
ms.reviewer: akjosh
ms.custom: akjosh, devx-track-azurecli
ms.openlocfilehash: b1833fe7c692529649e078e18e17e9333d74bfa6
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122693201"
---
# <a name="share-images-across-tenants-with-shared-image-gallery"></a>Partager des images entre les locataires avec Shared Image Gallery

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Machines virtuelles Windows :heavy_check_mark: Groupes identiques uniformes

[!INCLUDE [virtual-machines-share-images-across-tenants](../../includes/virtual-machines-share-images-across-tenants.md)]


## <a name="create-a-scale-set-using-azure-cli"></a>Créer un groupe identique avec Azure CLI

Connectez le principal du service pour le locataire 1 à l’aide de l’ID d’application, de la clé d’application et de l’ID du locataire 1. Vous pouvez utiliser `az account show --query "tenantId"` pour obtenir les ID des locataires si nécessaire.

```azurecli-interactive
az account clear
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 1 ID>'
az account get-access-token 
```
 
Connectez le principal du service pour le locataire 2 à l’aide de l’ID d’application, de la clé d’application et de l’ID du locataire 2 :

```azurecli-interactive
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 2 ID>'
az account get-access-token
```

Créez le groupe identique. Remplacez les informations dans l’exemple par vos propres données.

```azurecli-interactive
az vmss create \
  -g myResourceGroup \
  -n myScaleSet \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>Étapes suivantes

Si vous rencontrez des problèmes, consultez [Résoudre les problèmes des galeries d’images partagées](../virtual-machines/troubleshooting-shared-images.md).
