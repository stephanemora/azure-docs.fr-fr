---
author: dominicbetts
ms.author: dominicbetts
ms.service: iot-develop
ms.topic: include
ms.date: 11/15/2019
ms.openlocfilehash: c4e4d6858ce03c0a7015f6754018c509c738f8da
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/18/2021
ms.locfileid: "114400037"
---
Si vous prévoyez de continuer avec d’autres articles IoT Plug-and-Play, vous pouvez conserver et réutiliser les ressources que vous avez utilisées dans cet article. Sinon, vous pouvez supprimer les ressources que vous avez créées dans cet article afin d’éviter des frais supplémentaires.

Vous pouvez supprimer simultanément le hub et l’appareil inscrit en supprimant la totalité du groupe de ressources avec la commande Azure CLI suivante. N’utilisez pas cette commande si ces ressources partagent un groupe de ressources avec d’autres ressources que vous souhaitez conserver.

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
