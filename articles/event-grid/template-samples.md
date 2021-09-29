---
title: 'Exemples de modèles Azure Resource Manager : Event Grid | Microsoft Docs'
description: Cet article fournit une liste d’exemples de modèles Azure Resource Manager pour Azure Event Grid sur GitHub.
ms.topic: sample
ms.date: 09/28/2021
ms.openlocfilehash: 3c2188e99e62a1db3f71b4064a09535bc24c0c68
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129210824"
---
# <a name="azure-resource-manager-templates-for-event-grid"></a>Modèles Azure Resource Manager pour Event Grid

Pour connaître la syntaxe JSON et les propriétés à utiliser dans un modèle, consultez [Types de ressources Microsoft.EventGrid](/azure/templates/microsoft.eventgrid/allversions). Le tableau suivant inclut des liens vers des modèles Azure Resource Manager pour Event Grid.

## <a name="event-grid-subscriptions"></a>Abonnements Event Grid
- [Rubrique personnalisée et abonnement avec un point de terminaison webhook](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.eventgrid/event-grid) : déploie une rubrique personnalisée Event Grid. Crée un abonnement à cette rubrique personnalisée, qui utilise un point de terminaison WebHook. 
- [Abonnement à une rubrique personnalisée avec un point de terminaison EventHub](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.eventgrid/event-grid-event-hubs-handler) : crée un abonnement EventHub à une rubrique personnalisée. L’abonnement utilise un Hub d’événements pour le point de terminaison. 
- [Abonnement Azure ou abonnement à un groupe de ressources](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.eventgrid/event-grid-resource-events-to-webhook) : permet de s’abonner aux événements d’un groupe de ressources ou d’un abonnement Azure. Le groupe de ressources que vous spécifiez comme cible au cours du déploiement constitue la source d’événements. L’abonnement utilise un webhook pour le point de terminaison. 
- [Compte de stockage Blob et abonnement](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.eventgrid/event-grid-subscription-and-storage) : déploie un compte de stockage Blob Azure et s’abonne aux événements de ce compte de stockage. 

## <a name="next-steps"></a>Étapes suivantes
Consultez les exemples suivants :

- [Exemples PowerShell](powershell-samples.md)
- [Exemples d’interface de ligne de commande](cli-samples.md)
