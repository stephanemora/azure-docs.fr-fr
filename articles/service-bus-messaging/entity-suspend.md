---
title: Azure Service Bus – Interruption d’entités de messagerie
description: Cet article explique comment suspendre temporairement et réactiver des entités de message Azure Service Bus (files d’attente, rubriques et abonnements).
ms.topic: article
ms.date: 09/29/2020
ms.openlocfilehash: f89e17e494cc777691b7f7ca47538cd29114d2dc
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91575232"
---
# <a name="suspend-and-reactivate-messaging-entities-disable"></a>Interrompre et réactiver des entités de messagerie (désactiver)

Les files d’attente, rubriques et abonnements peuvent être temporairement interrompus. L’interruption place l’entité dans un état désactivé dans lequel tous les messages sont conservés dans le stockage. Toutefois, aucun message ne peut être supprimé ou ajouté, et les opérations de protocole respectif produisent des erreurs.

Généralement, une entité est interrompue pour des raisons administratives urgentes. Voici un exemple de scénario : le déploiement d’un récepteur défectueux qui récupère les messages de la file d’attente, ne parvient pas à les traiter et les complète de façon incorrecte et les supprime. Si ce comportement est diagnostiqué, la file d’attente peut être désactivée pour les réceptions jusqu’à ce que le code corrigé soit déployé et que les pertes de données entraînées par le code erroné puissent être évitées.

Aussi bien l’utilisateur que le système peuvent effectuer une interruption ou une réactivation. Le système interrompt uniquement les entités pour des raisons administratives graves comme atteindre la limite de dépense de l’abonnement. Les entités désactivées par le système ne peuvent pas être réactivées par l’utilisateur, mais sont restaurées une fois la cause de l’interruption traitée.

## <a name="queue-status"></a>État de la file d’attente 
Les états pouvant être définis pour une file d’attente sont :

-   **Actif** : la file d’attente est active.
-   **Disabled** : la file d’attente est suspendue. Cela revient à définir **SendDisabled** et **ReceiveDisabled**. 
-   **SendDisabled** : la file d’attente est partiellement suspendue, mais les réceptions sont autorisées.
-   **ReceiveDisabled** : la file d’attente est partiellement suspendue, mais les envois sont autorisés.

### <a name="change-the-queue-status-in-the-azure-portal"></a>Modifiez l’état de la file d’attente dans le portail Azure : 

1. Dans le portail Azure, accédez à votre espace de noms Service Bus. 
1. Sélectionnez la file d’attente dont vous souhaitez modifier l’état. Les files d’attente sont affichées au milieu du volet inférieur. 
1. Sur la page **File d’attente Service Bus**, examinez l’état actuel de la file d’attente sous forme de lien hypertexte. Si l’option **Vue d’ensemble** n’est pas sélectionnée dans le menu de gauche, sélectionnez-la pour afficher l’état de la file d’attente. Sélectionnez l’état actuel de la file d’attente pour le modifier. 

    :::image type="content" source="./media/entity-suspend/select-state.png" alt-text="Sélectionner l’état de la file d’attente":::
4. Sélectionnez le nouvel état de la file d’attente, puis **OK**. 

    :::image type="content" source="./media/entity-suspend/entity-state-change.png" alt-text="Sélectionner l’état de la file d’attente":::
    
Le portail vous permet uniquement de désactiver complètement les files d’attente. Vous pouvez également désactiver les opérations d’envoi et de réception séparément à l’aide de l’API [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) Service Bus dans le Kit de développement logiciel (SDK) .NET Framework ou avec un modèle Azure Resource Manager via Azure CLI ou Azure PowerShell.

### <a name="change-the-queue-status-using-azure-powershell"></a>Modifier l’état de la file d’attente à l’aide d’Azure PowerShell
La commande PowerShell pour désactiver une file d’attente est présentée dans l’exemple suivant. La commande de réactivation est équivalente et définit le paramètre `Status` sur **Active**.

```powershell
$q = Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue

$q.Status = "Disabled"

Set-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue -QueueObj $q
```

## <a name="topic-status"></a>État de la rubrique
Pour modifier l’état d’une rubrique dans le portail Azure, la procédure est la même que pour modifier l’état d’une file d’attente. Lorsque vous sélectionnez l’état actuel de la rubrique, la page suivante s’affiche et vous pouvez modifier l’état. 

:::image type="content" source="./media/entity-suspend/topic-state-change.png" alt-text="Sélectionner l’état de la file d’attente":::

Les états pouvant être définis pour une rubrique sont :
- **Actif** : La rubrique est active.
- **Disabled** : La rubrique est interrompue.
- **SendDisabled** : Même effet que **Désactivé**.

## <a name="subscription-status"></a>État de l’abonnement
Pour modifier l’état d’un abonnement dans le portail Azure, la procédure est la même que pour modifier l’état d’une rubrique ou d’une file d’attente. Lorsque vous sélectionnez l’état actuel de l’abonnement, la page suivante s’affiche et vous pouvez modifier l’état. 

:::image type="content" source="./media/entity-suspend/subscription-state-change.png" alt-text="Sélectionner l’état de la file d’attente":::

Les états pouvant être définis pour une rubrique sont :
- **Actif** : La rubrique est active.
- **Disabled** : La rubrique est interrompue.
- **ReceiveDisabled** : Même effet que **Désactivé**.

## <a name="other-statuses"></a>Autres états
L’énumération [EntityStatus](/dotnet/api/microsoft.servicebus.messaging.entitystatus) définit également un ensemble d’état de transition qui peuvent uniquement être configurés par le système. 


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la messagerie Service Bus, consultez les articles suivants :

* [Files d’attente, rubriques et abonnements Service Bus](service-bus-queues-topics-subscriptions.md)
* [Prise en main des files d’attente Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Utilisation des rubriques et abonnements Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/entity-suspend/entity-state-change.png

