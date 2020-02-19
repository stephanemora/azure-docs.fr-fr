---
title: Obtenir des notifications concernant la maintenance à l’aide de l’interface CLI
description: Affichez les notifications de maintenance pour les machines virtuelles s’exécutant dans Azure et démarrez la maintenance en libre-service à l’aide d’Azure CLI.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: 5750d7cc6219714849aaf3e47b23708b54eefab1
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77115713"
---
# <a name="handling-planned-maintenance-notifications-using-the-azure-cli"></a>Gestion de notifications de maintenance planifiée à l’aide d’Azure CLI

**Cet article s’applique aux machines virtuelles exécutant Linux et Windows.**

Vous pouvez utiliser l’interface CLI pour voir quand les machines virtuelles sont planifiées pour [maintenance](maintenance-notifications.md). Les informations relatives à la maintenance planifiée sont disponibles à l’aide de [az vm get-instance-view](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-get-instance-view).
 
Les informations de maintenance sont retournées uniquement en cas de maintenance planifiée. 

```azurecli-interactive
az vm get-instance-view -n myVM -g myResourceGroup --query instanceView.maintenanceRedeployStatus
```

## <a name="start-maintenance"></a>Démarrer la maintenance

L’appel suivant lance la maintenance sur une machine virtuelle si `IsCustomerInitiatedMaintenanceAllowed` est défini sur true.

```azurecli-interactive
az vm perform-maintenance -g myResourceGroup -n myVM 
```

## <a name="classic-deployments"></a>Déploiements Classic


Si vous avez encore des machines virtuelles qui ont été déployées à l’aide du modèle de déploiement Classic, vous pouvez utiliser l’interface Azure Classic CLI pour interroger ces machines virtuelles et lancer la maintenance.

Vérifiez que vous êtes dans le mode approprié pour utiliser une machine virtuelle Classic en tapant :

```
azure config mode asm
```

Pour obtenir l’état de la maintenance d’une machine virtuelle nommée *myVM*, tapez :

```
azure vm show myVM 
``` 

Pour démarrer la maintenance sur votre machine virtuelle classique dénommée *myVM* dans le service *myService* et le déploiement *myDeployment*, tapez :

```
azure compute virtual-machine initiate-maintenance --service-name myService --name myDeployment --virtual-machine-name myVM
```

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez également gérer la maintenance planifiée à l’aide d’[Azure PowerShell](maintenance-notifications-powershell.md) ou du [portail](maintenance-notifications-portal.md).
