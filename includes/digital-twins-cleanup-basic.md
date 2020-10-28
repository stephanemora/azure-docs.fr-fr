---
author: baanders
description: fichier include pour le nettoyage d’une instance Azure Digital Twins de base
ms.service: digital-twins
ms.topic: include
ms.date: 8/13/2020
ms.author: baanders
ms.openlocfilehash: 2e095a7b286a8860535d6b58fa93098735b30c87
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92372313"
---
Si vous n’avez plus besoin des ressources créées dans ce tutoriel, effectuez les étapes suivantes pour les supprimer.

Dans [Azure Cloud Shell](https://shell.azure.com), vous pouvez supprimer toutes les ressources Azure d’un groupe de ressources avec la commande [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest&preserve-view=true#az-group-delete). Cela supprime le groupe de ressources et l’instance Azure Digital Twins.

> [!IMPORTANT]
> La suppression d’un groupe de ressources est irréversible. Le groupe de ressources et toutes les ressources qu’il contient sont supprimés définitivement. Veillez à ne pas supprimer accidentellement des ressources ou un groupe de ressources incorrects. 

Ouvrez Azure Cloud Shell et exécutez la commande suivante pour supprimer le groupe de ressources et tout ce qu’il contient.

```azurecli
az group delete --name <your-resource-group>
```