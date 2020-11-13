---
author: baanders
description: fichier include pour le nettoyage d’une instance Azure Digital Twins de base
ms.service: digital-twins
ms.topic: include
ms.date: 8/13/2020
ms.author: baanders
ms.openlocfilehash: ab179c5f0c56a7d6fd2ebd41e13d27a832512b54
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358074"
---
Si vous n’avez plus besoin des ressources créées dans ce tutoriel, effectuez les étapes suivantes pour les supprimer.

Dans [Azure Cloud Shell](https://shell.azure.com), vous pouvez supprimer toutes les ressources Azure d’un groupe de ressources avec la commande [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest&preserve-view=true#az-group-delete). Cette commande supprime le groupe de ressources et l’instance Azure Digital Twins.

> [!IMPORTANT]
> La suppression d’un groupe de ressources est irréversible. Le groupe de ressources et toutes les ressources qu’il contient sont supprimés définitivement. Veillez à ne pas supprimer accidentellement les mauvaises ressources ou le mauvais groupe de ressources.

Ouvrez Azure Cloud Shell et exécutez la commande suivante pour supprimer le groupe de ressources et tout ce qu’il contient.

```azurecli-interactive
az group delete --name <your-resource-group>
```