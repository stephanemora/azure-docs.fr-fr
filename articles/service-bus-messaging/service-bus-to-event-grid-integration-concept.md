---
title: Vue d’ensemble de l’intégration d’Azure Service Bus et Event Grid | Documents Microsoft
description: Cet article décrit la manière dont la messagerie Azure Service Bus s’intègre à Azure Event Grid.
documentationcenter: .net
author: spelluru
ms.topic: conceptual
ms.date: 02/11/2021
ms.author: spelluru
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 999bfb9278f3c355e2b431c0fe3ca13648aa42c9
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/21/2021
ms.locfileid: "112412994"
---
# <a name="azure-service-bus-to-event-grid-integration-overview"></a>Vue d’ensemble de l’intégration d’Azure Service Bus et Event Grid
Service Bus peut maintenant émettre des événements vers Event Grid lorsque la file d’attente ou l’abonnement contient des messages et qu’aucun récepteur n’est présent. Vous pouvez créer des abonnements Event Grid à vos espaces de noms Service Bus, écouter ces événements et y réagir en démarrant un récepteur. Avec cette fonctionnalité, vous pouvez utiliser Service Bus dans des modèles de programmation réactive. Cette fonctionnalité peut être utilisée lorsque les abonnements ou les files d’attente Service Bus ayant un faible volume de messages n’ont pas besoin d’un récepteur qui interroge les messages en continu. 

Pour activer la fonctionnalité, vous avez besoin des éléments suivants :

* Un espace de noms Service Bus Premium avec au moins une file d’attente Service Bus ou une rubrique Service Bus avec au moins un abonnement.
* Un accès Contributeur à l’espace de noms Service Bus. Accédez à votre espace de noms Service Bus dans le portail Azure, sélectionnez **Contrôle d’accès (IAM)** , puis sélectionnez l’onglet **Attributions de rôles**. Vérifiez que vous disposez d’un accès Contributeur à l’espace de noms. 
* En outre, vous avez besoin d’un abonnement Event Grid pour l’espace de noms Service Bus. Cet abonnement reçoit une notification de la part d’Event Grid lui indiquant qu’il y a des messages à relever. Les abonnés classiques peuvent être la fonctionnalité Logic Apps d’Azure App Service, Azure Functions ou un webhook contactant une application web. L’abonné traite ensuite les messages. 

![19][]

[!INCLUDE [event-grid-service-bus.md](./includes/event-grid-service-bus.md)]

## <a name="event-grid-subscriptions-for-service-bus-namespaces"></a>Abonnements Event Grid pour les espaces de noms Service Bus
Il existe trois méthodes pour créer des abonnements Event Grid pour les espaces de noms Service Bus :

- Portail Azure. Consultez les tutoriels suivants afin de savoir comment utiliser Portail Azure pour créer des abonnements Event Grid pour des événements Service Bus avec Azure Logic Apps et Azure Functions comme gestionnaires. 
    - [Azure Logic Apps](service-bus-to-event-grid-integration-example.md#receive-messages-by-using-logic-apps)
    - [Azure Functions](service-bus-to-event-grid-integration-function.md#connect-the-function-and-namespace-via-event-grid)
* Azure CLI. L’exemple CLI suivant montre comment créer un abonnement Azure Functions pour une [rubrique système](../event-grid/system-topics.md) créée par un espace de noms Service Bus.

     ```azurecli-interactive
    namespaceid=$(az resource show --namespace Microsoft.ServiceBus --resource-type namespaces --name "<service bus namespace>" --resource-group "<resource group that contains the service bus namespace>" --query id --output tsv
    
    az eventgrid event-subscription create --resource-id $namespaceid --name "<YOUR EVENT GRID SUBSCRIPTION NAME>" --endpoint "<your_endpoint_url>" --subject-ends-with "<YOUR SERVICE BUS SUBSCRIPTION NAME>"
    ```
- PowerShell. Voici un exemple :
    ```powershell-interactive
    $namespaceID = (Get-AzServiceBusNamespace -ResourceGroupName "<YOUR RESOURCE GROUP NAME>" -NamespaceName "<YOUR NAMESPACE NAME>").Id
    
    New-AzEVentGridSubscription -EventSubscriptionName "<YOUR EVENT GRID SUBSCRIPTION NAME>" -ResourceId $namespaceID -Endpoint "<YOUR ENDPOINT URL>” -SubjectEndsWith "<YOUR SERVICE BUS SUBSCRIPTION NAME>"
    ```
### <a name="how-many-events-are-emitted-and-how-often"></a>À quelle fréquence et en quelle quantité sont émis les événements ?

Si plusieurs files d’attente et rubriques ou abonnements sont présents dans l’espace de noms, vous obtenez au moins un événement par file d’attente et un par abonnement. Les événements sont émis immédiatement s’il n’y a aucun message dans l’entité Service Bus et un nouveau message arrive. Ou les événements sont émis toutes les deux minutes, sauf si Service Bus détecte un récepteur actif. Le parcours des messages n’interrompt pas les événements.

Par défaut, Service Bus émet des événements pour toutes les entités dans l’espace de noms. Si vous souhaitez obtenir des événements uniquement pour des entités spécifiques, consultez la section suivante.

### <a name="use-filters-to-limit-where-you-get-events-from"></a>Utilisation de filtres pour limiter l’emplacement des événements que vous obtenez

Si vous souhaitez obtenir les événements d’une seule file d’attente ou d’un seul abonnement dans votre espace de noms, vous pouvez utiliser les filtres *Commence par* ou *Se termine par* fournis par Event Grid. Dans certaines interfaces, les filtres sont appelés des filtres de *Préfixe* et de *Suffixe*. Si vous souhaitez obtenir les événements de plusieurs files d’attente et abonnements, mais pas tous, vous pouvez créer plusieurs abonnements Event Grid et fournir un filtre pour chacun.

## <a name="next-steps"></a>Étapes suivantes
Consultez les didacticiels suivants : 
- [Azure Logic Apps pour gérer les messages Service Bus reçus via Event Grid](service-bus-to-event-grid-integration-example.md#receive-messages-by-using-logic-apps)
- [Azure Functions pour gérer les messages Service Bus reçus via Event Grid](service-bus-to-event-grid-integration-function.md#connect-the-function-and-namespace-via-event-grid)

[1]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgrid1.png
[19]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgriddiagram.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png
