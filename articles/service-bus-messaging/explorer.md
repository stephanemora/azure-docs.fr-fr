---
title: Utiliser Azure Service Bus Explorer pour effectuer des opérations de données sur Service Bus (préversion)
description: Cet article fournit des informations sur l’utilisation de l’Azure Service Bus Explorer basé sur le portail pour accéder à des données Azure Service Bus.
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: eb6610945ba4b09543308ab351d3a36ec7e587dd
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98684839"
---
# <a name="use-service-bus-explorer-to-perform-data-operations-on-service-bus-preview"></a>Utiliser l’Explorateur Service Bus pour effectuer des opérations de données sur Service Bus (préversion)

## <a name="overview"></a>Vue d’ensemble

Azure Service Bus permet aux applications clientes expéditrices et réceptrices de dissocier leur logique métier à l’aide de la sémantique familière de point à point (File d’attente) et de publication-abonnement (Rubrique-Abonnement).

Les opérations effectuées sur un espace de noms Azure Service Bus sont de deux sortes 
   * Opérations de gestion : création, mise à jour, suppression d’espace de noms, de files d’attente, de rubriques et d’abonnements Service Bus.
   * Opérations de données : échange de messages avec des files d’attente, des rubriques et des abonnements.

L’Explorateur Azure Service Bus étend la fonctionnalité du portail au-delà des opérations de gestion pour prendre en charge les opérations de données (envoi, réception, aperçu) sur les files d’attente, rubriques et abonnements (et leurs sous-entités de lettres mortes), directement à partir du portail Azure.

> [!NOTE]
> Cet article met en évidence la fonctionnalité de l’Explorateur Azure Service Bus qui se trouve sur le Portail Azure.
>
> L’outil Explorateur Azure Service Bus ne correspond ***pas** _ à l’outil OSS [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer) de la communauté.
>

## <a name="prerequisites"></a>Prérequis

Pour utiliser l’outil Service Bus Explorer, vous devez effectuer les tâches suivantes : 

- Provisionnez un espace de noms Azure Service Bus.
- Créez une file d’attente pour échanger des messages avec une rubrique associée à un abonnement afin de tester la fonctionnalité. Pour savoir comment créer des files d’attente, des rubriques et des abonnements, consultez les articles suivants : 
    - [Démarrage rapide – Créer des files d’attente](service-bus-quickstart-portal.md)
    - [Démarrage rapide – Créer des rubriques](service-bus-quickstart-topics-subscriptions-portal.md)
- Vérifiez que vous êtes membre de l’un de ces rôles sur l’espace de noms : 
    - [Propriétaire des données Service Bus](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner) 
    - [Contributeur](../role-based-access-control/built-in-roles.md#contributor) 
    - [Propriétaire](/role-based-access-control/built-in-roles.md#owner)


## <a name="using-the-service-bus-explorer"></a>Utilisation de Service Bus Explorer

Pour utiliser Azure Service Bus Explorer, vous devez accéder à l’espace de noms Service Bus sur lequel vous souhaitez effectuer des opérations d’envoi, d’aperçu et d’affichage.

Si vous cherchez à effectuer des opérations sur une file d’attente, choisissez _ *Files d’attente** dans le menu de navigation. Si vous cherchez à effectuer des opérations sur une rubrique (et ses abonnements associés), choisissez **Rubriques**. 

:::image type="content" source="./media/service-bus-explorer/queue-topics-left-navigation.png"alt-text="Sélection d’entité":::

Après avoir choisi **Files d’attente** ou **Rubriques**, sélectionnez la file d’attente ou la rubrique souhaitée.

Sélectionnez **Service Bus Explorer (préversion)** dans le menu de navigation de gauche.

:::image type="content" source="./media/service-bus-explorer/left-navigation-menu-selected.png" alt-text="Menu de navigation gauche de Service Bus Explorer":::

### <a name="sending-a-message-to-a-queue-or-topic"></a>Envoi d’un message à une file d’attente ou à une rubrique

Pour envoyer un message à une **File d’attente** ou à une **Rubrique**, cliquez sur l’onglet **_Envoyer_* _ dans Service Bus Explorer.

Pour composer un message ici : 

1. Choisissez le _ *Type de contenu** « Texte/brut », « Application/XML » ou « Application/JSON ».
2. Ajoutez le **Contenu** du message. Assurez-vous qu’il correspond au **Type de contenu** défini précédemment.
3. Définissez les **Propriétés avancées** (facultatif) : ID de corrélation, ID de message, Étiquette, ReplyTo, Durée de vie (TTL) et Heure planifiée de mise en file d’attente (pour les Messages planifiés).
4. Définissez les **Propriétés personnalisées**. Il peut s’agir de toute propriété utilisateur définie par rapport à une clé de dictionnaire.

Une fois le message composé, appuyez sur Envoyer.

:::image type="content" source="./media/service-bus-explorer/send-experience.png" alt-text="Composer un message":::

Lorsque l’opération d’envoi s’est terminée avec succès, 

* En cas d’envoi à la file d’attente, le compteur de métriques **Messages actifs** est incrémenté.

    :::image type="content" source="./media/service-bus-explorer/queue-after-send-metrics.png" alt-text="QueueAfterSendMetrics":::

* En cas d’envoi à la Rubrique, le compteur de métriques **Messages actifs** est incrémenté sur l’abonnement vers lequel le message a été acheminé.

    :::image type="content" source="./media/service-bus-explorer/topic-after-send-metrics.png" alt-text="TopicAfterSendMetrics":::

### <a name="receiving-a-message-from-a-queue"></a>Réception d’un message d’une file d’attente

La fonction de réception sur Service Bus Explorer permet de recevoir un seul message à la fois. L’opération de réception s’effectue à l’aide du mode **ReceiveAndDelete**.

> [!IMPORTANT]
> Notez que l’opération de réception effectuée par Service Bus Explorer est une **_réception destructrice_* _, c’est-à-dire que le message est supprimé de la file d’attente quand il est affiché dans l’outil Service Bus Explorer.
>
> Pour parcourir les messages sans les supprimer de la file d’attente, vous pouvez utiliser la fonctionnalité _*_Aperçu_*_.
>

Pour recevoir un message d’une File d’attente (ou de sa sous-file d’attente de lettres mortes) : 

1. Cliquez sur l’onglet _*_Recevoir_*_ de Service Bus Explorer.
2. Regardez s’il existe des _ *Messages actifs** ou des **Messages de lettres mortes** à recevoir dans les métriques.

    :::image type="content" source="./media/service-bus-explorer/queue-after-send-metrics.png" alt-text="QueueAfterSendMetrics":::

3. Choisissez entre la **_File d’attente_* _ et la sous-file d’attente _*_Lettre morte_*_.

    :::image type="content" source="./media/service-bus-explorer/queue-or-deadletter.png" alt-text="QueueOrDeadletter":::

4. Cliquez sur le bouton _*_Recevoir_*_, puis sur _*_Oui_*_ pour confirmer l’opération « Recevoir et supprimer ».


Lorsque l’opération de réception aboutit, les détails du message s’affichent dans la grille comme ci-dessous. Vous pouvez sélectionner le message dans la grille pour afficher ses détails.

:::image type="content" source="./media/service-bus-explorer/receive-message-from-queue-2.png" alt-text="Capture d’écran de la fenêtre Files d’attente dans Azure Service Bus Explorer, avec les détails d’un message actif sélectionné dans la file d’attente":::


### <a name="peeking-a-message-from-a-queue"></a>Affichage d’aperçu d’un message d’une file d’attente

La fonctionnalité d’aperçu vous permet d’utiliser Service Bus Explorer pour afficher les 32 premiers messages d’une file d’attente ou la file d’attente de lettres mortes.

1. Pour afficher un aperçu du message dans une file d’attente, cliquez sur l’onglet _*_Aperçu_*_ dans Service Bus Explorer.

    :::image type="content" source="./media/service-bus-explorer/peek-tab-selected.png" alt-text="PeekTab":::

2. Regardez s’il existe des _ *Messages actifs** ou des **Messages de lettres mortes** à afficher dans les métriques.

    :::image type="content" source="./media/service-bus-explorer/queue-after-send-metrics.png" alt-text="QueueAfterSendMetrics":::

3. Choisissez ensuite entre la **_File d’attente_* _ et la sous-file d’attente _*_Lettre morte_*_.

    :::image type="content" source="./media/service-bus-explorer/queue-or-deadletter.png" alt-text="QueueOrDeadletter":::

4. Cliquez sur le bouton _*_Aperçu_*_. 

Une fois l’opération d’affichage de l’aperçu terminée, jusqu’à 32 messages s’affichent dans la grille comme ci-dessous. Pour afficher les détails d’un message particulier, sélectionnez-le dans la grille. 

:::image type="content" source="./media/service-bus-explorer/peek-message-from-queue-2.png" alt-text="PeekMessageFromQueue":::

> [!NOTE]
>
> Comme l’aperçu n’est pas une opération destructrice, le message n’est _ *pas** supprimé de la file d’attente.
>

### <a name="receiving-a-message-from-a-subscription"></a>Réception d’un message d’un abonnement

Comme pour une file d’attente, l’opération **_Recevoir_* _ peut être effectuée sur un abonnement (ou son entité de lettres mortes). Toutefois, étant donné qu’un abonnement se trouve dans le contexte de la rubrique, l’opération de réception est effectuée en accédant au Service Bus Explorer pour une rubrique donnée.

> [!IMPORTANT]
> Notez que l’opération de réception effectuée par Service Bus Explorer est une _*_réception destructrice_*_, c’est-à-dire que le message est supprimé de la file d’attente quand il est affiché dans l’outil Service Bus Explorer.
>
> Pour parcourir les messages sans les supprimer de la file d’attente, vous pouvez utiliser la fonctionnalité _*_Aperçu_*_.
>

1. Cliquez sur l’onglet _*_Recevoir_*_ et sélectionnez _*_l’Abonnement_*_ spécifique dans le sélecteur de liste déroulante.

    :::image type="content" source="./media/service-bus-explorer/receive-subscription-tab-selected.png" alt-text="ReceiveTabSelected":::

2. Choisissez entre _*_l’Abonnement_*_ et la sous-entité _*_Lettres mortes_*_.

    :::image type="content" source="./media/service-bus-explorer/subscription-or-deadletter.png" alt-text="SubscriptionOrDeadletter":::

3. Cliquez sur le bouton _*_Recevoir_*_, puis sur _*_Oui_*_ pour confirmer l’opération « Recevoir et supprimer ».

Lorsque l’opération de réception aboutit, le message reçu s’affiche dans la grille comme ci-dessous. Pour afficher les détails du message, cliquez dessus.

:::image type="content" source="./media/service-bus-explorer/receive-message-from-subscription.png" alt-text="Capture d’écran de l’onglet Recevoir dans Azure Service Bus Explorer, avec les détails d’un message actif qui a été reçu":::

### <a name="peeking-a-message-from-a-subscription"></a>Affichage de l’aperçu d’un message d’un abonnement

Pour parcourir simplement les messages d’un abonnement ou de sa sous-entité de lettres mortes, la fonctionnalité _*_Aperçu_*_ peut également être utilisée sur l’abonnement.

1. Cliquez sur l’onglet _*_Aperçu_*_ et sélectionnez _*_l’Abonnement_*_ spécifique dans le sélecteur de liste déroulante.

    :::image type="content" source="./media/service-bus-explorer/peek-subscription-tab-selected.png" alt-text="PeekTabSelected":::

2. Choisissez entre _*_l’Abonnement_*_ et la sous-entité _*_Lettres mortes_*_.

    :::image type="content" source="./media/service-bus-explorer/subscription-or-deadletter.png" alt-text="SubscriptionOrDeadletter":::

3. Cliquez sur le bouton _*_Aperçu_*_.

Une fois l’opération d’affichage de l’aperçu terminée, jusqu’à 32 messages s’affichent dans la grille comme ci-dessous. Pour afficher les détails d’un message particulier, sélectionnez-le dans la grille. 

:::image type="content" source="./media/service-bus-explorer/peek-message-from-subscription.png" alt-text="PeekMessageFromSubscription":::

> [!NOTE]
>
> Comme l’aperçu n’est pas une opération destructrice, le message n’est _ *pas** supprimé de la file d’attente.
>

## <a name="next-steps"></a>Étapes suivantes

   * Apprenez-en davantage sur les [Files d’attente](service-bus-queues-topics-subscriptions.md#queues) et les [Rubriques](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) Service Bus
   * Apprenez-en davantage sur la création de [Files d’attente Service Bus via le portail Azure](service-bus-quickstart-portal.md)
   * Apprenez-en davantage sur la création de [Rubriques et abonnements Service Bus via le portail Azure](service-bus-quickstart-topics-subscriptions-portal.md)