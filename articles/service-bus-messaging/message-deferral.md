---
title: Azure Service Bus - Report des messages
description: Cet article explique comment reporter la remise des messages Azure Service Bus. Le message est conservé dans la file d’attente ou l’abonnement, mais il est mis de côté.
ms.topic: conceptual
ms.date: 04/21/2021
ms.openlocfilehash: 71446f7989a235e79ca3ab208579edd0043dfeb2
ms.sourcegitcommit: aba63ab15a1a10f6456c16cd382952df4fd7c3ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2021
ms.locfileid: "107987251"
---
# <a name="message-deferral"></a>Report de message
Lorsqu’un client de file d’attente ou d’abonnement reçoit un message qu’il souhaite traiter, mais que le traitement n’est pas possible actuellement en raison de circonstances particulières, il a la possibilité de « reporter » la récupération du message. Le message est conservé dans la file d’attente ou l’abonnement, mais il est mis de côté.

> [!NOTE]
> Les messages différés ne sont pas automatiquement déplacés vers la file d’attente de lettres mortes [après leur expiration](./service-bus-dead-letter-queues.md#time-to-live). Ce comportement est lié à la conception.

## <a name="sample-scenarios"></a>Exemples de scénarios
Le report est une fonctionnalité créée spécifiquement pour des scénarios de traitement de workflows. Les infrastructures de workflow peuvent nécessiter que certaines opérations soient traitées dans un ordre particulier. Elles peuvent être amenées à retarder le traitement de certains messages reçus jusqu’à ce que le travail antérieur prescrit, informé par d’autres messages, soit terminé.

Un exemple simple illustrant cela est une séquence de traitement d’une commande dans laquelle une notification de paiement provenant d’un fournisseur de paiement externe s’affiche dans un système avant que le bon de commande associé ait été transmis du magasin au système de traitement. Dans ce cas, le système de traitement peut reporter le traitement de la notification de paiement jusqu’à ce qu’une commande y soit associée. Dans les scénarios de rendez-vous, dans lesquels des messages provenant de différentes sources font avancer un flux de travail, l’ordre d’exécution en temps réel peut effectivement être correct, mais les messages reflétant les résultats peuvent arriver dans le désordre.

Enfin, le report permet de faciliter la réorganisation des messages, de leur ordre d’arrivée à l’ordre dans lequel ils peuvent être traités, tout en laissant en toute sécurité ces messages (dont le traitement doit être différé) dans le magasin de messages.

Si un message ne peut pas être traité en raison de l’indisponibilité temporaire d’une ressource nécessaire au traitement de ce message, mais que le traitement du message ne doit pas être sommairement interrompu, un moyen pour mettre le message de côté pendant quelques minutes consiste à se souvenir du numéro de séquence dans un [message planifié](message-sequencing.md) qui sera publié après quelques minutes, puis à récupérer le message reporté lorsque le message planifié arrive. Si un gestionnaire de messages dépend d’une base de données pour toutes les opérations et que cette base de données est temporairement indisponible, il ne doit pas utiliser le report, mais plutôt interrompre complètement la réception des messages jusqu’à ce que la base de données soit à nouveau disponible. 

## <a name="retrieving-deferred-messages"></a>Récupération des messages reportés
Les messages reportés restent dans la file d’attente principale avec tous les autres messages actifs (contrairement aux messages de type lettres mortes qui se trouvent dans une sous-file d’attente), mais ils ne peuvent plus être reçus à l’aide des opérations de réception habituelles. Les messages reportés peuvent être détectés via la [navigation dans les messages](message-browsing.md) si une application perd leur trace.

Pour récupérer un message reporté, son propriétaire doit se souvenir du numéro de séquence au moment du report. Tout destinataire qui connaît le numéro de séquence d’un message reporté peut ultérieurement recevoir le message en utilisant les méthodes de réception qui prennent le numéro de séquence comme paramètre. Pour plus d’informations sur les numéros de séquence, consultez [Séquencement et horodatage des messages](message-sequencing.md).

## <a name="next-steps"></a>Étapes suivantes
Essayez les exemples dans le langage de votre choix pour explorer les fonctionnalités d’Azure Service Bus. 

- [Exemples de bibliothèque de client Azure Service Bus pour .NET (dernière version)](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/) : Voir l’exemple **Règlement des messages**. 
- [Exemples de bibliothèque de client Azure Service Bus pour Java (dernière version)](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Exemples de bibliothèque de client Azure Service Bus pour Python](/samples/azure/azure-sdk-for-python/servicebus-samples/) : Voir l’exemple **receive_deferred_message_queue.py**. 
- [Exemples de bibliothèque de client Azure Service Bus pour JavaScript](/samples/azure/azure-sdk-for-js/service-bus-javascript/) : Voir l’exemple **advanced/deferral.js**. 
- [Exemples de bibliothèque de client Azure Service Bus pour TypeScript](/samples/azure/azure-sdk-for-js/service-bus-typescript/) : Voir l’exemple **advanced/deferral.ts**. 

Recherchez des exemples pour les anciennes bibliothèques clientes .NET et Java ci-dessous :
- [Exemples de bibliothèque de client Azure Service Bus pour .NET (version héritée)](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/) : Voir l’exemple **Deferral**. 
- [Exemples de bibliothèque de client Azure Service Bus pour Java (version héritée)](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/MessageBrowse)
