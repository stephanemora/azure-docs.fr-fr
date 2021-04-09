---
author: dominicbetts
ms.author: dominicbetts
ms.service: iot-pnp
ms.topic: include
ms.date: 11/15/2019
ms.openlocfilehash: 647226091c3e15a19bf8262c23e62d95018488b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99831199"
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
