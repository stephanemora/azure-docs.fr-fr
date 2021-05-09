---
title: Azure Service Bus – Exploration des messages
description: Le parcours et l’aperçu des messages Service Bus permettent à un client Azure Service Bus d’énumérer tous les messages d’une file d’attente ou d’un abonnement.
ms.topic: article
ms.date: 03/29/2021
ms.openlocfilehash: 23ec2187ae58b1b4f83addb80573bdb130ff99bd
ms.sourcegitcommit: aba63ab15a1a10f6456c16cd382952df4fd7c3ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2021
ms.locfileid: "107989393"
---
# <a name="message-browsing"></a>Parcours des messages
Le parcours des messages, ou « Peek », permet à un client Service Bus d’énumérer tous les messages d’une file d’attente ou d’un abonnement, à des fins de diagnostic et de débogage.

L’opération Peek sur une file d’attente ou un abonnement retourne le plus grand nombre de messages demandé. Le tableau suivant indique les types de messages retournés par l’opération Peek. 

| Type de message | Inclus ? | 
| ---------------- | ----- | 
| Messages actifs | Oui |
| Messages de lettres mortes | Non | 
| Messages verrouillés | Oui |
| Messages arrivés à expiration |  Peut être (avant mise en file d’attente des messages non distribués) |
| Messages planifiés | Oui pour les files d’attente. Non pour les abonnements |

## <a name="dead-lettered-messages"></a>Messages de lettres mortes
Pour afficher un aperçu des messages **non distribués mis en file d’attente** d’une file d’attente ou d’un abonnement, l’opération Peek doit être exécutée sur la file d’attente des messages non distribués associée à la file d’attente ou à l’abonnement. Pour plus d’informations, consultez [Accès aux files d’attente de messages non distribués](service-bus-dead-letter-queues.md#path-to-the-dead-letter-queue).

## <a name="expired-messages"></a>Messages arrivés à expiration
Les messages arrivés à expiration peuvent être inclus dans les résultats retournés par l’opération Peek. Les messages utilisés et arrivés à expiration sont nettoyés par une exécution « nettoyage de la mémoire » asynchrone. Cette étape ne se produit pas nécessairement immédiatement après l’expiration des messages. C’est la raison pour laquelle une opération Peek peut renvoyer des messages qui ont déjà expiré. Ces messages seront supprimés ou mis en file d’attente de lettres mortes la prochaine fois qu’une opération de réception sera appelée sur la file d’attente ou l’abonnement. Gardez ce comportement à l’esprit lorsque vous tentez de récupérer des messages différés de la file d’attente. 

Un message arrivé à expiration n’est plus éligible pour la récupération normale par d’autres moyens, même quand il est retourné par Peek. Le renvoi de ces messages est délibéré, car Peek est un outil de diagnostic reflétant l’état actuel du journal.

## <a name="locked-messages"></a>Messages verrouillés
Peek renvoie également les messages qui ont été **verrouillés** et qui sont actuellement traités par d’autres destinataires. Toutefois, étant donné que Peek renvoie un instantané déconnecté, l’état de verrou d’un message ne peut pas être observé sur des messages aperçus.

## <a name="peek-apis"></a>API de l’outil Peek
Peek fonctionne sur les files d’attente, les abonnements et leurs files d’attente de messages non distribués respectives. 

Lorsqu’elle est appelée à plusieurs reprises, l’opération Peek énumère tous les messages de la file d’attente ou de l’abonnement, du plus petit numéro de séquence disponible au plus grand. C’est l’ordre dans lequel les messages ont été empilés, et non l’ordre dans lequel ils peuvent être récupérés au final.

Vous pouvez également transmettre un SequenceNumber à une opération Peek. Il permet de déterminer où commencer l’aperçu. Vous pouvez effectuer des appels ultérieurs à l’opération Peek sans spécifier le paramètre à énumérer.

## <a name="next-steps"></a>Étapes suivantes
Essayez les exemples dans le langage de votre choix pour explorer les fonctionnalités d’Azure Service Bus. 

- [Exemples de bibliothèque de client Azure Service Bus pour .NET (dernière version)](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/) - **Envoi et réception de messages** - Exemple
- [Exemples de bibliothèque de client Azure Service Bus pour Java (dernière version)](/samples/azure/azure-sdk-for-java/servicebus-samples/) - **Aperçu d'un message** - Exemple
- [Exemples de bibliothèque de client Azure Service Bus pour Python](/samples/azure/azure-sdk-for-python/servicebus-samples/)  - **receive_peek. py** - Exemple
- [Exemples de bibliothèque de client Azure Service Bus pour JavaScript](/samples/azure/azure-sdk-for-js/service-bus-javascript/)  - **browseMessages.js** - Exemple
- [Exemples de bibliothèque de client Azure Service Bus pour TypeScript](/samples/azure/azure-sdk-for-js/service-bus-typescript/) - **browseMessages.ts** - Exemple

Recherchez des exemples pour les anciennes bibliothèques clientes .NET et Java ci-dessous :
- [Exemples de bibliothèque de client Azure Service Bus pour .NET (version héritée)](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/) - **Exploration des messages (Aperçu)** - Exemple
- [Exemples de bibliothèque de client Azure Service Bus pour Java (version héritée)](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus) - **Exploration des messages** - Exemple 

