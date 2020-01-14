---
author: baanders
ms.author: baanders
ms.service: iot-pnp
ms.topic: include
ms.date: 11/15/2019
ms.openlocfilehash: d88d166e001e71cabdabc3a3d344adc4da19aa8c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453687"
---
## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous prévoyez de continuer avec d’autres articles IoT Plug-and-Play, vous pouvez conserver et réutiliser les ressources que vous avez utilisées dans ce guide de démarrage rapide. Dans le cas contraire, vous pouvez supprimer les ressources que vous avez créées dans ce guide de démarrage rapide afin d’éviter des frais supplémentaires.

Vous pouvez supprimer simultanément le hub et l’appareil inscrit en supprimant la totalité du groupe de ressources avec la commande suivante pour Azure CLI. (Ne l’utilisez cependant pas si ces ressources partagent un groupe de ressources avec d’autres ressources que vous utilisez pour d’autres raisons.)

```azurecli-interactive
az group delete --name <YourResourceGroupName>
```
Pour supprimer seulement le hub IoT, exécutez la commande suivante en utilisant Azure CLI :

```azurecli-interactive
az iot hub delete --name <YourIoTHubName>
```

Pour supprimer seulement l’appareil que vous avez inscrit auprès de votre hub IoT, exécutez la commande suivante en utilisant Azure CLI :

```azurecli-interactive
az iot hub device-identity delete --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Vous pouvez aussi supprimer les exemples de fichiers clonés de votre ordinateur de développement.