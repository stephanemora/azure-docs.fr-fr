---
title: Azure Service Bus – Nombre de messages
description: Récupérez le nombre de messages contenus dans les files d’attente et les abonnements à l’aide d’Azure Resource Manager et des API NamespaceManager d’Azure Service Bus.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 3ef2e50521b57fc45846acecf9e776e3ff24c5d4
ms.sourcegitcommit: aba63ab15a1a10f6456c16cd382952df4fd7c3ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2021
ms.locfileid: "107987323"
---
# <a name="get-message-counters"></a>Obtenir des compteurs de messages
Cet article vous montre différentes façons d’obtenir les nombres de messages suivants pour une file d’attente ou un abonnement. Le fait de connaître le nombre de messages actifs permet de déterminer si une file d’attente génère un backlog dont le traitement nécessite davantage de ressources que celles déployées actuellement. 

| Compteur | Description |
| ----- | ---------- | 
| ActiveMessageCount | Nombre de messages dans la file d’attente ou l’abonnement qui sont à l’état actif et prêts à être remis. |
| ScheduledMessageCount | Nombre de messages à l’état planifié. |
| DeadLetterMessageCount | Nombre de messages dans la file d’attente de lettres mortes. |
| TransferMessageCount | Nombre de messages en attente de transfert vers une autre file d’attente ou rubrique. |
| TransferDeadLetterMessageCount | Nombre de messages dont le transfert vers une autre file d’attente ou rubrique a échoué et qui ont été déplacés vers la file d’attente de lettres mortes de transfert. |

Si une application a besoin de mettre à l’échelle les ressources en fonction de la longueur de la file d’attente, elle doit le faire progressivement. L’acquisition des compteurs de messages est une opération onéreuse dans le répartiteur de messages, dont l’exécution fréquente compromet directement les performances des entités.

> [!NOTE]
> Les messages envoyés à une rubrique Service Bus sont transférés aux abonnements pour cette rubrique. Par conséquent, le nombre de messages actifs sur la rubrique elle-même est 0, car ces messages ont été transférés à l’abonnement. Récupérez le nombre de messages au niveau de l’abonnement et vérifiez qu’il est supérieur à 0. Même si vous voyez des messages au niveau de l’abonnement, ils se trouvent en réalité dans un stockage appartenant à la rubrique. Si vous examinez les abonnements, ils doivent comptabiliser un nombre de messages différent de zéro (ce qui représente jusqu’à 323 Mo d’espace pour l’ensemble de cette entité).


## <a name="using-azure-portal"></a>En passant par le portail Azure
Accédez à votre espace de noms, puis sélectionnez la file d’attente. Les compteurs de messages s’affichent sur la page **Vue d’ensemble** de la file d’attente.

:::image type="content" source="./media/message-counters/queue-overview.png" alt-text="Compteurs de messages sur la page de présentation de la file d’attente":::

Accédez à votre espace de noms, sélectionnez la rubrique, puis sélectionnez l’abonnement pour la rubrique. Les compteurs de messages s’affichent sur la page **Vue d’ensemble** de la file d’attente.

:::image type="content" source="./media/message-counters/subscription-overview.png" alt-text="Compteurs de messages sur la page de présentation de l’abonnement":::

## <a name="using-azure-cli"></a>Utilisation de l’interface de ligne de commande Azure
Utilisez la commande [`az servicebus queue show`](/cli/azure/servicebus/queue#az_servicebus_queue_show) pour obtenir les détails du nombre de messages pour une file d’attente, comme indiqué dans l’exemple suivant. 

```azurecli-interactive
az servicebus queue show --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --query countDetails
```

Voici un exemple de sortie :

```bash
ActiveMessageCount    DeadLetterMessageCount    ScheduledMessageCount    TransferMessageCount    TransferDeadLetterMessageCount
--------------------  ------------------------  -----------------------  ----------------------  --------------------------------
0                     0                         0                        0                       0
```

Utilisez la commande [`az servicebus topic subscription show`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_show) pour obtenir les détails du nombre de messages pour un abonnement, comme indiqué dans l’exemple suivant. 

```azurecli-interactive
az servicebus topic subscription show --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysub \
    --query countDetails
```

## <a name="using-azure-powershell"></a>Utilisation de Microsoft Azure PowerShell
Avec PowerShell, vous pouvez obtenir les détails du nombre de messages pour une file d’attente comme suit :

```azurepowershell-interactive
$queueObj=Get-AzServiceBusQueue -ResourceGroup myresourcegroup `
                    -NamespaceName mynamespace `
                    -QueueName myqueue 

$queueObj.CountDetails
```

Voici l'exemple de sortie :

```bash
ActiveMessageCount             : 7
DeadLetterMessageCount         : 1
ScheduledMessageCount          : 3
TransferMessageCount           : 0
TransferDeadLetterMessageCount : 0
```

Vous pouvez obtenir les détails du nombre de messages pour un abonnement comme suit :

```azurepowershell-interactive
$topicObj= Get-AzServiceBusSubscription -ResourceGroup myresourcegroup `
                -NamespaceName mynamespace `
                -TopicName mytopic `
                -SubscriptionName mysub

$topicObj.CountDetails
```

L’objet `MessageCountDetails` renvoyé comporte les propriétés suivantes : `ActiveMessageCount`, `DeadLetterMessageCount`, `ScheduledMessageCount`, `TransferDeadLetterMessageCount`, `TransferMessageCount`. 

## <a name="next-steps"></a>Étapes suivantes

Essayez les exemples dans le langage de votre choix pour explorer les fonctionnalités d’Azure Service Bus. 

- [Exemples de bibliothèque de client Azure Service Bus pour .NET (dernière version)](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/) 
- [Exemples de bibliothèque de client Azure Service Bus pour Java (dernière version)](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Exemples de bibliothèque de client Azure Service Bus pour Python](/samples/azure/azure-sdk-for-python/servicebus-samples/)
- [Exemples de bibliothèque de client Azure Service Bus pour JavaScript](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [Exemples de bibliothèque de client Azure Service Bus pour TypeScript](/samples/azure/azure-sdk-for-js/service-bus-typescript/)

Recherchez des exemples pour les anciennes bibliothèques clientes .NET et Java ci-dessous :
- [Exemples de bibliothèque de client Azure Service Bus pour .NET (version héritée)](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/)
- [Exemples de bibliothèque de client Azure Service Bus pour Java (version héritée)](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus)
