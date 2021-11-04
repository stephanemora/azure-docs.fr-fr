---
ms.topic: include
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: 633121c21bf09c608c0c40d6b87bf68edfbaf1f8
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131479129"
---
## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous n’en avez plus besoin, nettoyez les ressources Azure que vous avez déployées en supprimant le groupe de ressources. 

### <a name="clean-up-resources-using-the-azure-portal"></a>Nettoyer des ressources à l’aide du portail Azure

Supprimez les ressources dans le portail Azure en suivant les étapes décrites dans [Nettoyer les ressources](../ingest-data/data-explorer-ingest-event-hub-portal.md#clean-up-resources).

### <a name="clean-up-resources-using-powershell"></a>Supprimer des ressources à l’aide de PowerShell

Si Cloud Shell est toujours ouvert, vous n’avez pas besoin de copier/exécuter la première ligne (Read-Host).

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```
