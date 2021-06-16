---
title: Obtenir une clé d’accès pour une ressource Event Grid
description: Cet article explique comment obtenir une clé d'accès pour une rubrique ou un domaine Event Grid
ms.topic: how-to
ms.date: 07/07/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6052c93dc91403fccaed62719b163c24486557c5
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110702530"
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
Utilisez la [liste de clés de rubrique Azure Event Grid](/cli/azure/eventgrid/topic/key#az_eventgrid_topic_key_list) pour obtenir les clés d’accès pour des rubriques. 

```azurecli-interactive
az eventgrid topic key list --resource-group <RESOURCE GROUP NAME> --name <TOPIC NAME>
```

Utilisez la [liste de clés de domaine Azure Event Grid](/cli/azure/eventgrid/domain/key#az_eventgrid_domain_key_list) pour obtenir les clés d’accès pour des domaines. 

```azurecli-interactive
az eventgrid domain key list --resource-group <RESOURCE GROUP NAME> --name <DOMAIN NAME>
```

## <a name="next-steps"></a>Étapes suivantes
Consultez l’article suivant : [Authentifier les clients de publication](security-authenticate-publishing-clients.md). 
