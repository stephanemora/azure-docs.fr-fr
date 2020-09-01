---
author: baanders
description: fichier include pour le nettoyage d’une instance Azure Digital Twins de base et l’inscription d’une application
ms.service: digital-twins
ms.topic: include
ms.date: 8/13/2020
ms.author: baanders
ms.openlocfilehash: 45d6a806e94ceca9574b0ed5f73c2b87ef438438
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88891491"
---
Si vous n’avez plus besoin des ressources créées dans ce tutoriel, effectuez les étapes suivantes pour les supprimer.

Dans [Azure Cloud Shell](https://shell.azure.com), vous pouvez supprimer toutes les ressources Azure d’un groupe de ressources avec la commande [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete). Cela supprime le groupe de ressources et l’instance Azure Digital Twins.

> [!IMPORTANT]
> La suppression d’un groupe de ressources est irréversible. Le groupe de ressources et toutes les ressources qu’il contient sont supprimés définitivement. Veillez à ne pas supprimer accidentellement des ressources ou un groupe de ressources incorrects. 

Ouvrez Azure Cloud Shell et exécutez la commande suivante pour supprimer le groupe de ressources et tout ce qu’il contient.

```azurecli-interactive
az group delete --name <your-resource-group>
```

Ensuite, supprimez l’inscription d’application Azure Active Directory que vous avez créée pour votre application cliente à l’aide de cette commande :

```azurecli-interactive
az ad app delete --id <your-application-ID>
```