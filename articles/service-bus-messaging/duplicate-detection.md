---
title: Détection des messages dupliqués dans Azure Service Bus | Microsoft Docs
description: Cet article explique la façon dont vous pouvez détecter les doublons dans les messages Azure Service Bus. Le message en double peut être ignoré et abandonné.
ms.topic: article
ms.date: 04/19/2021
ms.openlocfilehash: baeda3509cb5646c658f79fb11610ecfdd1ffd3d
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751271"
---
# <a name="duplicate-detection"></a>Détection des doublons

Si une application échoue en raison d’une erreur irrécupérable immédiatement après avoir envoyé un message, et que l’instance d’application redémarrée considère à tort que le message précédent n’a pas été remis, l’opération d’envoi suivante entraîne le réaffichage du même message dans le système.

Il est également possible qu’une erreur se produise sur le client ou le réseau à un moment antérieur, et qu’un message envoyé soit confirmé dans la file d’attente, avec l’accusé de réception non retourné au client. Dans ce scénario, le client ne connait pas avec certitude le résultat de l’opération d’envoi.

La détection des doublons lève ce type d’incertitude en permettant à l’expéditeur de renvoyer le même message, et à la file d’attente ou la rubrique d’ignorer les copies en double.

> [!NOTE]
> Le niveau De base de Service Bus ne prend pas en charge la détection des doublons. Les niveaux Standard et Premium prennent en charge la détection des doublons. Pour connaître les différences entre ces niveaux, voir [Tarification de Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).

## <a name="how-it-works"></a>Fonctionnement 
L’activation de la détection des doublons vous aide à effectuer le suivi du *MessageId*, contrôlé par l’application, de chaque message envoyé dans une file d’attente ou une rubrique pendant une fenêtre de temps spécifiée. Si un nouveau message est envoyé avec un *MessageId* journalisé pendant la fenêtre de temps, le message est signalé comme étant accepté (réussite de l’opération d’envoi), mais le message qui vient d’être envoyé est instantanément ignoré et supprimé. Excepté le *MessageId*, aucune autre partie du message n’est prise en compte.

Le contrôle de l’identificateur par l’application est essentiel, car lui seul permet à l’application d’associer l’identificateur *MessageId* à un contexte de processus métier à partir duquel il peut être reconstruit en cas d’échec.

Pour un processus métier dans lequel plusieurs messages sont envoyés durant le traitement d’un contexte d’application, *MessageId* peut se composer de l’identificateur de contexte de l’application, par exemple un numéro de bon de commande, et de l’objet du message, par exemple, **12345.2017/paiement**.

*MessageId* peut aussi être un GUID, mais associer l’identificateur au processus métier offre l’avantage de fournir une répétabilité prévisible, ce qui permet de tirer pleinement parti de la fonctionnalité de détection des doublons.

> [!IMPORTANT]
>- Lorsque le **partitionnement** est **activé**, `MessageId+PartitionKey` sert à déterminer l’unicité. Lorsque des sessions sont activées, la clé de partition et l’ID de session doivent être identiques. 
>- Lorsque le **partitionnement** est **désactivé** (par défaut), seul `MessageId` sert à déterminer l’unicité.
>- Pour plus d’informations sur SessionId, PartitionKey et MessageId, consultez [Utilisation de clés de partition](service-bus-partitioning.md#use-of-partition-keys).
>- Le [niveau Premier](service-bus-premium-messaging.md) ne prend pas en charge le partitionnement. Nous vous recommandons donc d’utiliser des ID de message uniques dans vos applications et de ne pas utiliser de clés de partition pour la détection des doublons. 


## <a name="duplicate-detection-window-size"></a>Taille de la fenêtre de détection des doublons

Outre l’activation de la détection des doublons, vous pouvez également configurer la taille de la fenêtre de temps de l’historique de détection des doublons pour laquelle les ID de message sont conservés.
Cette valeur est de 10 minutes par défaut pour les files d’attente et les rubriques, avec une valeur minimale de 20 secondes et une valeur maximale de 7 jours.

L’activation de la détection des doublons et la taille de la fenêtre ont un impact direct sur le débit des files d’attente (et des rubriques), car tous les ID de messages enregistrés doivent être vérifiés par rapport à l’identificateur de message qui vient d’être envoyé.

En maintenant la fenêtre à une petite taille, vous avez moins d’ID de messages à conserver et à vérifier, et l’impact sur le débit reste ainsi limité. Pour les entités à débit élevé qui nécessitent la détection des doublons, essayez de garder la fenêtre aussi petite que possible.

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez activer la détection des message en doublon en utilisant le portail Azure, PowerShell, l’interface CLI, un modèle Resource Manager, .NET, Java, Python et JavaScript. Pour plus d’informations, consultez [Activer la détection des messages en doublon](enable-duplicate-detection.md). 

Dans les scénarios où le code client ne peut pas renvoyer de message avec le même *MessageId* que précédemment, il est important de concevoir des messages qui peuvent être retraités en toute sécurité. Ce [billet de blog sur l’idempotence](https://particular.net/blog/what-does-idempotent-mean) décrit diverses techniques permettant de le faire.

Essayez les exemples dans le langage de votre choix pour explorer les fonctionnalités d’Azure Service Bus. 

- [Exemples de bibliothèque de client Azure Service Bus pour Java](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Exemples de bibliothèque de client Azure Service Bus pour Python](/samples/azure/azure-sdk-for-python/servicebus-samples/)
- [Exemples de bibliothèque de client Azure Service Bus pour JavaScript](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [Exemples de bibliothèque de client Azure Service Bus pour TypeScript](/samples/azure/azure-sdk-for-js/service-bus-typescript/)
- [Exemples Azure.Messaging.ServiceBus pour .NET](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/)

Trouvez des exemples pour les anciennes bibliothèques de client .NET et Java ci-dessous :
- [Exemples Microsoft.Azure.ServiceBus pour .NET](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/)
- [Exemples azure-servicebus pour Java](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/MessageBrowse)

