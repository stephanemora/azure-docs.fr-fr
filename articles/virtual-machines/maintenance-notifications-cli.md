---
title: Obtenir des notifications concernant la maintenance à l’aide de l’interface CLI
description: Affichez les notifications de maintenance pour les machines virtuelles s’exécutant dans Azure et démarrez la maintenance en libre-service à l’aide d’Azure CLI.
author: shants123
ms.service: virtual-machines
ms.subservice: maintenance-control
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: d8a9b7ec6425a3cd32b597c3f14f8227fde67064
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777866"
---
# <a name="handling-planned-maintenance-notifications-using-the-azure-cli"></a>Gestion de notifications de maintenance planifiée à l’aide d’Azure CLI

**Cet article s’applique aux machines virtuelles exécutant Linux et Windows.**

Vous pouvez utiliser l’interface CLI pour voir quand les machines virtuelles sont planifiées pour [maintenance](maintenance-notifications.md). Les informations relatives à la maintenance planifiée sont disponibles à l’aide de [az vm get-instance-view](/cli/azure/vm#az_vm_get_instance_view).
 
Les informations de maintenance sont retournées uniquement en cas de maintenance planifiée. 

```azurecli-interactive
az vm get-instance-view -n myVM -g myResourceGroup --query instanceView.maintenanceRedeployStatus
```

Output
```
      "maintenanceRedeployStatus": {
      "additionalProperties": {},
      "isCustomerInitiatedMaintenanceAllowed": true,
      "lastOperationMessage": null,
      "lastOperationResultCode": "None",
      "maintenanceWindowEndTime": "2018-06-04T16:30:00+00:00",
      "maintenanceWindowStartTime": "2018-05-21T16:30:00+00:00",
      "preMaintenanceWindowEndTime": "2018-05-19T12:30:00+00:00",
      "preMaintenanceWindowStartTime": "2018-05-14T12:30:00+00:00"
```

## <a name="start-maintenance"></a>Démarrer la maintenance

L’appel suivant lance la maintenance sur une machine virtuelle si `IsCustomerInitiatedMaintenanceAllowed` est défini sur true.

```azurecli-interactive
az vm perform-maintenance -g myResourceGroup -n myVM 
```

## <a name="classic-deployments"></a>Déploiements Classic

[!INCLUDE [classic-vm-deprecation](../../includes/classic-vm-deprecation.md)]

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
