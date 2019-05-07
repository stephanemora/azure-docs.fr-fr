---
title: Partager des images de la galerie sur les locataires dans Azure | Microsoft Docs
description: Découvrez comment partager des images de machine virtuelle entre les clients Azure à l’aide des galeries d’images partagé.
services: virtual-machine-scale-sets
author: cynthn
manager: jeconnoc
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/05/2019
ms.author: cynthn
ms.openlocfilehash: cbaaac629fd013602eed75cc7dc357f13a62e3b1
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65160124"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Partager des images de machine virtuelle de la galerie sur les clients Azure

[!INCLUDE [virtual-machines-share-images-across-tenants](../../includes/virtual-machines-share-images-across-tenants.md)]


## <a name="create-a-scale-set-using-azure-cli"></a>Créer un groupe identique à l’aide d’Azure CLI

Le principal du service pour le client 1 à l’aide de l’appID, la clé d’application et l’ID de locataire 1 se connecter. Vous pouvez utiliser `az account show --query "tenantId"` pour obtenir les ID de locataire si nécessaire.

```azurecli-interactive
az account clear
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 1 ID>'
az account get-access-token 
```
 
Le principal du service pour le client à l’aide de l’appID, la clé d’application et l’ID de client 2 de 2 la connexion :

```azurecli-interactive
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 2 ID>'
az account get-access-token
```

Créez le groupe identique. Remplacez les informations contenues dans l’exemple par les vôtres.

```azurecli-interactive
az vmss create \
  -g myResourceGroup \
  -n myScaleSet \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>Étapes suivantes

Si vous rencontrez des problèmes, consultez [Résoudre les problèmes des galeries d’images partagées](troubleshooting-shared-images.md).