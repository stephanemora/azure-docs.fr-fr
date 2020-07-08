---
title: Azure Service Bus – Interruption d’entités de messagerie
description: Cet article explique comment suspendre temporairement et réactiver des entités de message Azure Service Bus (files d’attente, rubriques et abonnements).
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 4c4e554ada8c66b56d5d466c7becfc813701402a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85339992"
---
# <a name="suspend-and-reactivate-messaging-entities-disable"></a>Interrompre et réactiver des entités de messagerie (désactiver)

Les files d’attente, rubriques et abonnements peuvent être temporairement interrompus. L’interruption place l’entité dans un état désactivé dans lequel tous les messages sont conservés dans le stockage. Toutefois, aucun message ne peut être supprimé ou ajouté, et les opérations de protocole respectif produisent des erreurs.

Généralement, une entité est interrompue pour des raisons administratives urgentes. Voici un exemple de scénario : le déploiement d’un récepteur défectueux qui récupère les messages de la file d’attente, ne parvient pas à les traiter et les complète de façon incorrecte et les supprime. Si ce comportement est diagnostiqué, la file d’attente peut être désactivée pour les réceptions jusqu’à ce que le code corrigé soit déployé et que les pertes de données entraînées par le code erroné puissent être évitées.

Aussi bien l’utilisateur que le système peuvent effectuer une interruption ou une réactivation. Le système interrompt uniquement les entités pour des raisons administratives graves comme atteindre la limite de dépense de l’abonnement. Les entités désactivées par le système ne peuvent pas être réactivées par l’utilisateur, mais sont restaurées une fois la cause de l’interruption traitée.

Dans le portail, la section **Propriétés** de l’entité respective permet de modifier l’état ; la capture d’écran suivante montre le bouton bascule d’une file d’attente :

![][1]

Le portail vous permet uniquement de désactiver complètement les files d’attente. Vous pouvez également désactiver les opérations d’envoi et de réception séparément à l’aide de l’API [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) Service Bus dans le Kit de développement logiciel (SDK) .NET Framework ou avec un modèle Azure Resource Manager via Azure CLI ou Azure PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="suspension-states"></a>États d’interruption

Les états pouvant être définis pour une file d’attente sont :

-   **Active** : la file d’attente est active.
-   **Désactivée** : la file d’attente est interrompue.
-   **SendDisabled** : la file d’attente est partiellement interrompue, mais les réceptions autorisées.
-   **ReceiveDisabled** : la file d’attente est partiellement interrompue, mais les envois autorisés.

Pour les abonnements et les rubriques, seuls les états **Active** et **Désactivée** peuvent être définis.

L’énumération [EntityStatus](/dotnet/api/microsoft.servicebus.messaging.entitystatus) définit également un ensemble d’état de transition qui peuvent uniquement être configurés par le système. La commande PowerShell pour désactiver une file d’attente est présentée dans l’exemple suivant. La commande de réactivation est équivalente et définit le paramètre `Status` sur **Active**.

```powershell
$q = Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue

$q.Status = "Disabled"

Set-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue -QueueObj $q
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la messagerie Service Bus, consultez les articles suivants :

* [Files d’attente, rubriques et abonnements Service Bus](service-bus-queues-topics-subscriptions.md)
* [Prise en main des files d’attente Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Utilisation des rubriques et abonnements Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/entity-suspend/queue-disable.png

