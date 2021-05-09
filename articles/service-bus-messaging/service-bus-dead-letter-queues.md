---
title: Files d’attente de lettres mortes Service Bus | Microsoft Docs
description: Décrit les files d’attente de lettres mortes dans Azure Service Bus. Les files d’attente et abonnements aux rubriques Service Bus fournissent une sous-file d’attente secondaire, appelée file d’attente de lettres mortes.
ms.topic: article
ms.date: 04/08/2021
ms.custom: fasttrack-edit, devx-track-csharp
ms.openlocfilehash: 6293a3a9a760ece137644578d8ee7dccebc63d95
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812369"
---
# <a name="overview-of-service-bus-dead-letter-queues"></a>Vue d’ensemble des files d’attente de lettres mortes Service Bus

Les files d’attente et abonnements aux rubriques Azure Service Bus fournissent une sous-file d’attente secondaire, appelée *file d’attente de lettres mortes*. La file d’attente de lettres mortes n’a pas besoin d’être explicitement créée et ne peut pas être supprimée ni gérée indépendamment de l’entité principale.

Cet article décrit les files d’attente de lettres mortes dans Service Bus. Une grande partie de la discussion est illustrée par l’[exemple de files d’attente de lettres mortes](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/DeadletterQueue) qui se trouve sur GitHub.
 
## <a name="the-dead-letter-queue"></a>La file d’attente de lettres mortes

L’objectif de la file d’attente de lettres mortes est de conserver les messages qui ne peuvent pas être remis aux destinataires ou les messages qui n’ont pas pu être traités. Les messages peuvent alors être supprimés de la file d’attente de lettres mortes et inspectés. Une application peut, à l’aide d’un opérateur, corriger les problèmes et renvoyer le message, consigner le fait qu’une erreur s’est produite et prendre des mesures correctives. 

Du point de vue de l’API et du protocole, la file d’attente de lettres mortes est essentiellement similaire à une autre file d’attente, sauf que les messages peuvent être envoyés uniquement par le biais de l’opération de lettres mortes de l’entité parente. En outre, la durée de vie n’est pas observée, et vous ne pouvez pas supprimer un message d’une file d’attente de lettres mortes. La file d’attente de lettres mortes prend entièrement en charge la remise de verrou d’affichage et les opérations transactionnelles.

Il n’y a aucun nettoyage automatique de la file d’attente de lettres mortes. Les messages restent dans la file d’attente de lettres mortes jusqu’à ce que vous les récupériez explicitement et que vous complétiez le message de lettres mortes.


## <a name="dlq-message-count"></a>Nombre de messages en file d’attente de lettres mortes
Il n’est pas possible de connaître le nombre de messages en file d’attente de lettres mortes au niveau de la rubrique. Cela est dû au fait que les messages ne se trouvent pas au niveau de la rubrique, sauf si Service Bus lève une erreur interne. Au lieu de cela, lorsqu’un expéditeur envoie un message à une rubrique, le message est transféré aux abonnements de la rubrique en quelques millisecondes et ne réside donc plus au niveau de la rubrique. Par conséquent, vous pouvez voir les messages dans la file d’attente de lettres mortes associée à l’abonnement de la rubrique. Dans l’exemple suivant, **Service Bus Explorer** indique qu’il y a actuellement 62  messages dans la file d’attente de lettres mortes pour l’abonnement « test1 ». 

![Nombre de messages en file d’attente de lettres mortes](./media/service-bus-dead-letter-queues/dead-letter-queue-message-count.png)

Vous pouvez également récupérer le nombre de messages en file d’attente de lettres mortes à l’aide de la commande Azure CLI : [`az servicebus topic subscription show`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_show). 

## <a name="moving-messages-to-the-dlq"></a>Déplacer des messages vers la file d’attente de lettres mortes
Plusieurs activités dans Service Bus entraînent l’envoi des messages dans la file d’attente de lettres mortes à partir du moteur de messagerie lui-même. Une application peut également explicitement déplacer des messages dans la file d’attente de lettres mortes. Les deux propriétés suivantes (motif de lettres mortes et description de lettres mortes) sont ajoutées aux messages de lettres mortes. Les applications peuvent définir leurs propres codes pour la propriété de motif de lettres mortes, mais le système définit les valeurs suivantes.

| Motif de lettres mortes | Description de l’erreur de lettres mortes |
| --- | --- |
|HeaderSizeExceeded |Le quota de taille pour ce flux a été dépassé. |
|TTLExpiredException |Le message a expiré et a été placé dans la file d’attente de lettres mortes. Pour plus d’informations, consultez la section relative à la [Durée de vie](#time-to-live). |
|L’ID de session a la valeur Null. |L’entité activée dans la session n’autorise pas les messages dont l’identificateur de session a la valeur null. |
|MaxTransferHopCountExceeded | Nombre maximal de sauts autorisés lors du transfert entre files d’attente. La valeur est définie sur 4. |
| MaxDeliveryCountExceededExceptionMessage | Le message n’a pas pu être consommé après un nombre de tentatives de livraison maximal. Pour plus d’informations, consultez la section [Nombre maximal de remises](#maximum-delivery-count). |

## <a name="maximum-delivery-count"></a>Nombre maximal de diffusions
Il existe une limite du nombre de tentatives de remise des messages pour files d’attente et les abonnements Service Bus. La valeur par défaut est 10. Chaque fois qu’un message a été remis sous un verrou, mais qu’il a été explicitement abandonné ou que le verrou a expiré, le compteur de remises du message est incrémenté. Lorsque le nombre de remises dépasse la limite, le message est déplacé vers la file d’attente de lettres mortes. Le motif de lettres mortes du message dans la file d’attente est défini sur : MaxDeliveryCountExceeded. Ce comportement ne peut pas être désactivé, mais vous pouvez définir le nombre maximal de diffusions sur un grand nombre.

## <a name="time-to-live"></a>Durée de vie
Lorsque vous activez les lettres mortes sur les files d’attente ou les abonnements, tous les messages arrivant à expiration sont déplacés vers la file d’attente de lettres mortes. Le code de motif de lettres mortes est défini sur : TTLExpiredException.

Les messages ayant expiré sont uniquement purgés et transférés vers la file d’attente de lettres mortes quand au moins un destinataire actif effectue une collecte à partir de la file d’attente principale ou l’abonnement. Les messages différés ne sont pas purgés et sont déplacés vers la file d’attente de lettres mortes après leur expiration. Ce comportement est normal.

## <a name="errors-while-processing-subscription-rules"></a>Erreurs pendant le traitement des règles d’abonnement
Si vous activez les lettres mortes sur les exceptions d’évaluation de filtre, toutes les erreurs qui se produisent pendant l’exécution d’une règle de filtre SQL d’un abonnement sont capturées dans la file d’attente de lettres mortes avec le message incriminé. N’utilisez pas cette option dans un environnement de production dans lequel certains types de messages n’ont pas d’abonnés.

## <a name="application-level-dead-lettering"></a>Mise en file d’attente de lettres mortes au niveau de l’application
En plus des fonctionnalités de file d’attente de lettres mortes fournies par le système, les applications peuvent utiliser la file d’attente de lettres mortes pour refuser explicitement les messages inacceptables. Il peut s’agir de messages qui ne peuvent pas être traités correctement en raison d’un problème système, de messages qui contiennent des charges utiles incorrectement formées ou de messages qui n’ont pas pu être authentifiés lors de l’utilisation d’un schéma de sécurité au niveau du message.

## <a name="dead-lettering-in-forwardto-or-sendvia-scenarios"></a>Mise en file d’attente de lettres mortes dans des scénarios ForwardTo ou SendVia
Les messages seront envoyés à la file d’attente de lettres mortes de transfert dans les conditions suivantes :

- Un message passe par plus de quatre files d’attente ou rubriques [enchaînées](service-bus-auto-forwarding.md).
- La file d’attente de destination ou la rubrique est désactivée ou supprimée.
- La file d’attente ou la rubrique de destination dépasse la taille d’entité maximale.

## <a name="path-to-the-dead-letter-queue"></a>Chemin d’accès à la file d’attente de lettres mortes
Vous pouvez accéder à la file d’attente de lettres mortes à l’aide de la syntaxe suivante :

```
<queue path>/$deadletterqueue
<topic path>/Subscriptions/<subscription path>/$deadletterqueue
```


## <a name="next-steps"></a>Étapes suivantes
Consultez [Activer la mise en file d’attente de lettres mortes pour une file d’attente ou un abonnement](enable-dead-letter.md) pour en savoir plus sur les différentes façons de configurer le paramètre de **mise en file d’attente de lettres mortes à l’expiration du message**.
