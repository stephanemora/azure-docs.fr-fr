---
title: Obtenir une clé d’accès pour une ressource Event Grid
description: Cet article explique comment obtenir une clé d'accès pour une rubrique ou un domaine Event Grid
ms.topic: how-to
ms.date: 07/07/2020
ms.openlocfilehash: a642affbac79766684dc75a37dae0373450d20e8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98632527"
---
# <a name="get-access-keys-for-event-grid-resources-topics-or-domains"></a>Obtenir des clés d’accès pour des ressources Event Grid (rubriques ou domaines)
Les clés d’accès sont utilisées pour authentifier les événements de publication d’une application dans des ressources Azure Event Grid (rubriques et domaines). Nous vous recommandons de regénérer régulièrement vos clés et de les stocker en sécurité. Vous disposez de deux clés d’accès, ce qui signifie que vous pouvez maintenir des connexions à l’aide d’une clé pendant que vous régénérez l’autre clé.

Cet article explique comment obtenir des clés d’accès pour une ressource Event Grid (rubrique ou domaine) à l’aide du portail Azure, de PowerShell ou de l’interface CLI. 

## <a name="azure-portal"></a>Portail Azure
Dans le portail Azure, basculez vers l’onglet **Clés d’accès** de la page **Rubrique Event Grid**  ou **Domaine Event Grid** pour votre rubrique ou domaine.  

:::image type="content" source="./media/get-access-keys/azure-portal.png" alt-text="Page clés d'accès":::

## <a name="azure-powershell"></a>Azure PowerShell
Utilisez la commande [Get-AzEventGridTopicKey](/powershell/module/az.eventgrid/get-azeventgridtopickey) pour obtenir des clés d’accès pour les rubriques. 

```azurepowershell-interactive
Get-AzEventGridTopicKey -ResourceGroup <RESOURCE GROUP NAME> -Name <TOPIC NAME>
```

Utilisez la commande [Get-AzEventGridDomainKey](/powershell/module/az.eventgrid/get-azeventgriddomainkey) pour obtenir les clés d’accès pour les domaines. 

```azurepowershell-interactive
Get-AzEventGridDomainKey -ResourceGroup <RESOURCE GROUP NAME> -Name <DOMAIN NAME>
```

## <a name="azure-cli"></a>Azure CLI
Utilisez la [liste de clés de rubrique Azure Event Grid](/cli/azure/eventgrid/topic/key#az-eventgrid-topic-key-list) pour obtenir les clés d’accès pour des rubriques. 

```azurecli-interactive
az eventgrid topic key list --resource-group <RESOURCE GROUP NAME> --name <TOPIC NAME>
```

Utilisez la [liste de clés de domaine Azure Event Grid](/cli/azure/eventgrid/domain/key#az-eventgrid-domain-key-list) pour obtenir les clés d’accès pour des domaines. 

```azurecli-interactive
az eventgrid domain key list --resource-group <RESOURCE GROUP NAME> --name <DOMAIN NAME>
```

## <a name="next-steps"></a>Étapes suivantes
Consultez l’article suivant : [Authentifier les clients de publication](security-authenticate-publishing-clients.md). 
