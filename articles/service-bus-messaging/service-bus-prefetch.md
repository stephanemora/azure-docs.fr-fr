---
title: Prérécupérer les messages Azure Service Bus | Microsoft Docs
description: Améliorez les performances en prérécupérant les messages Azure Service Bus. Les messages sont immédiatement récupérables localement, avant que l'application les demande.
ms.topic: article
ms.date: 04/23/2021
ms.openlocfilehash: 992fa9228d3cf8bafb3d2ea9110b515ff4b56498
ms.sourcegitcommit: aba63ab15a1a10f6456c16cd382952df4fd7c3ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2021
ms.locfileid: "107989087"
---
# <a name="prefetch-azure-service-bus-messages"></a>Prérécupérer les messages Azure Service Bus
Lorsque vous activez la fonctionnalité *Prérécupérer* pour l’un des clients Service Bus officiels, le destinataire acquiert plus de messages que ce que l’application a demandé initialement, jusqu’à concurrence du nombre de prérécupérations spécifié. Notez que les clients JavaScript et TypeScript ne prennent pas encore en charge cette fonctionnalité.

Au fur et à mesure que les messages sont renvoyés à l’application, le client acquiert d’autres messages en arrière-plan afin de remplir la mémoire tampon de prérécupération.

## <a name="enabling-prefetch"></a>Activation de la prérécupération
Pour activer la fonctionnalité Prérécupérer, définissez le nombre de prérécupérations du client de file d’attente ou d’abonnement sur un nombre supérieur à zéro. La définition de cette propriété sur la valeur zéro désactive la prérécupération. 

# <a name="net"></a>[.NET](#tab/dotnet)
Si vous utilisez la bibliothèque Azure.Messaging.ServiceBus la plus récente, vous pouvez définir la propriété de nombre de prérécupérations sur les objets [ServiceBusReceiver](/dotnet/api/azure.messaging.servicebus.servicebusreceiver.prefetchcount#Azure_Messaging_ServiceBus_ServiceBusReceiver_PrefetchCount) et [ServiceBusProcessor](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.prefetchcount#Azure_Messaging_ServiceBus_ServiceBusProcessor_PrefetchCount). 

Si vous utilisez l’ancienne bibliothèque de client .NET pour Service Bus (Microsoft.Azure.ServiceBus), vous pouvez définir la propriété de nombre de prérécupérations sur [MessageReceiver](/dotnet/api/microsoft.servicebus.messaging.messagereceiver.prefetchcount), [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount#Microsoft_Azure_ServiceBus_QueueClient_PrefetchCount) ou [SubscriptionClient](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.prefetchcount).
 
# <a name="java"></a>[Java](#tab/java)
Si vous utilisez la bibliothèque azure-messaging-servicebus la plus récente, vous pouvez définir la propriété de nombre de prérécupérations sur les objets [ServiceBusReceiver](/dotnet/api/azure.messaging.servicebus.servicebusreceiver.prefetchcount#Azure_Messaging_ServiceBus_ServiceBusReceiver_PrefetchCount) et [ServiceBusProcessor](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.prefetchcount#Azure_Messaging_ServiceBus_ServiceBusProcessor_PrefetchCount). 

Si vous utilisez l’ancienne bibliothèque de client Java pour Service Bus (azure-servicebus), vous pouvez définir la propriété de nombre de prérécupérations sur [MessageReceiver](/java/api/com.microsoft.azure.servicebus.imessagereceiver.setprefetchcount#com_microsoft_azure_servicebus_IMessageReceiver_setPrefetchCount_int_), [QueueClient](/java/api/com.microsoft.azure.servicebus.queueclient.setprefetchcount#com_microsoft_azure_servicebus_QueueClient_setPrefetchCount_int_) ou [SubscriptionClient](/java/api/com.microsoft.azure.servicebus.subscriptionclient.setprefetchcount#com_microsoft_azure_servicebus_SubscriptionClient_setPrefetchCount_int_).
 
# <a name="python"></a>[Python](#tab/python)

Vous pouvez définir **prefetch_count** sur [azure.servicebus.ServiceBusReceiver](/python/api/azure-servicebus/azure.servicebus.servicebusreceiver) ou [azure.servicebus.aio.ServiceBusReceiver](/python/api/azure-servicebus/azure.servicebus.aio.servicebusreceiver).

---

> [!NOTE]
> Le Kit de développement logiciel (SDK) JavaScript ne prend pas en charge la fonctionnalité **Prérécupérer**. 

Tant que les messages sont disponibles dans la mémoire tampon de prérécupération, tous les appels de réception ultérieurs sont immédiatement satisfaits à partir de la mémoire tampon. La mémoire tampon est réapprovisionnée en arrière-plan à mesure que de l’espace est disponible. Si plus aucun message n’est prêt à être remis, l’opération de réception vide la mémoire tampon, puis attend ou se bloque, comme attendu.

## <a name="why-is-prefetch-not-the-default-option"></a>Pourquoi la prérécupération n’est-elle pas l’option par défaut ?
La prérécupération accélère le flux de messages en faisant en sorte qu’un message soit immédiatement récupérable localement avant que l’application le demande. Ce gain de débit résulte d’un compromis que l’auteur de l’application doit effectuer de manière explicite :

Avec le mode [receive-and-delete](message-transfers-locks-settlement.md#receiveanddelete), tous les messages qui sont acquis dans la mémoire tampon de prérécupération ne sont plus disponibles dans la file d’attente. Les messages restent uniquement dans la mémoire tampon de prérécupération jusqu’à ce qu’ils soient reçus dans l’application. Si l’application se termine avant d’avoir reçu les messages, ces derniers sont irrécupérables (perdus).

En mode de réception [peek-lock](message-transfers-locks-settlement.md#peeklock), les messages récupérés dans la mémoire tampon de prérécupération sont acquis dans la mémoire tampon à l’état verrouillé. Ils déclenchent l’horloge de délai d’expiration du verrouillage. Si la mémoire tampon de prérécupération est volumineuse et que le traitement est si long que le verrouillage des messages arrive à expiration pendant que les messages résident dans la mémoire tampon de prérécupération ou même pendant que l’application traite les messages, l’application peut faire face à certains événements déroutants.

Il est possible que l’application acquière un message dont le verrouillage est arrivé à expiration ou doit l’être prochainement. Dans ce cas, l’application peut commencer à traiter le message, puis se trouver dans l’impossibilité d’achever le traitement à cause de l’expiration du verrouillage. L’application peut vérifier la propriété `LockedUntilUtc` (qui est soumise aux variations d’horloge entre l’horloge du répartiteur et celle de la machine locale). Si le verrouillage d’un message est arrivé à expiration, l’application doit ignorer le message, et ne doit passer aucun appel d’API sur le message. Si le message n’est pas arrivé à expiration, mais que son expiration est imminente, il est possible de renouveler et de prolonger le verrouillage avec une autre période de verrouillage par défaut.

Si le verrouillage arrive silencieusement à expiration dans la mémoire tampon de prérécupération, le message est considéré comme abandonné et redevient récupérable à partir de la file d’attente. Il se peut que le message soit récupéré dans la mémoire tampon de prérécupération et placé à la fin. Si la mémoire tampon de prérécupération n’est généralement pas utilisable pendant l’arrivée à expiration des messages, les messages sont prérécupérés de façon répétée, mais ne sont en fait jamais remis dans un état exploitable (correctement verrouillés), et sont finalement déplacés vers la file d’attente de lettres mortes une fois que le nombre de remises maximal a été atteint.

Si vous exigez un haut niveau de fiabilité pour le traitement des messages et que ce traitement demande des efforts et un temps considérables, nous vous recommandons d’utiliser la fonctionnalité Prérécupérer avec prudence ou de ne pas l’utiliser du tout.

Si vous avez besoin d’un haut débit et que votre processus de traitement des messages est généralement peu coûteux, la prérécupération entraîne des avantages substantiels en matière de débit.

Le nombre maximal de prérécupérations et la durée de verrouillage configurés sur la file d’attente ou l’abonnement doivent être équilibrés, de sorte que le délai d’expiration du verrouillage dépasse au moins le temps de traitement des messages prévu cumulé pour la taille maximale de la mémoire tampon de prérécupération, plus un message. Dans le même temps, le délai d’expiration du verrouillage ne doit pas être si long que les messages risquent de dépasser leur durée de vie maximum lorsqu’ils sont accidentellement abandonnés, ce qui nécessite d’attendre l’arrivée à expiration du verrouillage des messages pour que ces derniers puissent faire l’objet d’une nouvelle tentative de remise.

## <a name="next-steps"></a>Étapes suivantes

Essayez les exemples dans le langage de votre choix pour explorer les fonctionnalités d’Azure Service Bus. 

- [Exemples de bibliothèque de client Azure Service Bus pour .NET (dernière version)](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/) 
- [Exemples de bibliothèque de client Azure Service Bus pour Java (dernière version)](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Exemples de bibliothèque de client Azure Service Bus pour Python](/samples/azure/azure-sdk-for-python/servicebus-samples/)
- [Exemples de bibliothèque de client Azure Service Bus pour JavaScript](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [Exemples de bibliothèque de client Azure Service Bus pour TypeScript](/samples/azure/azure-sdk-for-js/service-bus-typescript/)

Recherchez des exemples pour les anciennes bibliothèques clientes .NET et Java ci-dessous :
- [Exemples de bibliothèque de client Azure Service Bus pour .NET (version héritée)](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/) : Voir l’exemple **Prefetch**. 
- [Exemples de bibliothèque de client Azure Service Bus pour Java (version héritée)](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus) : Voir l’exemple **Prefetch**. 
