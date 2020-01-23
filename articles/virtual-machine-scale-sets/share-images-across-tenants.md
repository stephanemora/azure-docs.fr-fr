---
title: Partager des images de la bibliothèque entre tenants dans Azure
description: Découvrez comment partager des images de machine virtuelle entre des locataires Azure à l’aide des galeries d’images partagées.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: cynthn
ms.openlocfilehash: a29999102ad8a10d8965145b31a7d804675e0e57
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2020
ms.locfileid: "76276337"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Partager des images de machine virtuelle de la galerie entre des locataires Azure

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

Si vous rencontrez des problèmes, consultez [Résoudre les problèmes des galeries d’images partagées](troubleshooting-shared-images.md).
