---
author: baanders
description: fichier include pour le nettoyage d’une instance Azure Digital Twins de base et l’inscription d’une application
ms.service: digital-twins
ms.topic: include
ms.date: 8/13/2020
ms.author: baanders
ms.openlocfilehash: e8f167555434338416381aaf8978a3d551686fb2
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/16/2020
ms.locfileid: "90606374"
---
Si vous n’avez plus besoin des ressources créées dans ce tutoriel, effectuez les étapes suivantes pour les supprimer.

Dans [Azure Cloud Shell](https://shell.azure.com), vous pouvez supprimer toutes les ressources Azure d’un groupe de ressources avec la commande [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest&preserve-view=true#az-group-delete). Cela supprime le groupe de ressources et l’instance Azure Digital Twins.

> [!IMPORTANT]
> La suppression d’un groupe de ressources est irréversible. Le groupe de ressources et toutes les ressources qu’il contient sont supprimés définitivement. Veillez à ne pas supprimer accidentellement des ressources ou un groupe de ressources incorrects. 

Ouvrez Azure Cloud Shell et exécutez la commande suivante pour supprimer le groupe de ressources et tout ce qu’il contient.

```azurecli
az group delete --name <your-resource-group>
```

Ensuite, supprimez l’inscription d’application Azure Active Directory que vous avez créée pour votre application cliente à l’aide de cette commande :

```azurecli
az ad app delete --id <your-application-ID>
```