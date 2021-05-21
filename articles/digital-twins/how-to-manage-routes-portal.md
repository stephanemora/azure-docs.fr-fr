---
title: Gérer les points de terminaison et les itinéraires (portail)
titleSuffix: Azure Digital Twins
description: Découvrez comment configurer et gérer des points de terminaison et des itinéraires d’événements pour les données d’Azure Digital Twins à l’aide du portail Azure.
author: baanders
ms.author: baanders
ms.date: 7/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: a7159b6528c07a2cb2734c06d644a4db3753d48a
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2021
ms.locfileid: "109790880"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins-portal"></a>Gérer les points de terminaison et les itinéraires dans Azure Digital Twins (portail)

[!INCLUDE [digital-twins-route-selector.md](../../includes/digital-twins-route-selector.md)]

Dans Azure Digital Twins, vous pouvez acheminer les [notifications d’événements](concepts-event-notifications.md) vers des services en aval ou des ressources de calcul connectées. Pour ce faire, vous devez d’abord configurer des **points de terminaison** qui peuvent recevoir les événements. Vous pouvez ensuite créer des [routes d’événements](concepts-route-events.md) qui spécifient quels événements générés par Azure Digital Twins doivent être envoyés aux points de terminaison.

Cet article vous guide tout au long du processus de création de points de terminaison et d’itinéraires à l’aide du [portail Azure](https://portal.azure.com).

Vous pouvez également gérer les points de terminaison et les routes à l’aide des [API de routes d’événements](/rest/api/digital-twins/dataplane/eventroutes), des kits [SDK](concepts-apis-sdks.md#overview-data-plane-apis) ou de l’[interface CLI Azure Digital Twins](concepts-cli.md). Pour obtenir une version de cet article qui utilise ces mécanismes plutôt que le portail, consultez [Guide pratique : Gérer les points de terminaison et les itinéraires (API et CLI)](how-to-manage-routes-apis-cli.md) .

## <a name="prerequisites"></a>Prérequis

* Vous aurez besoin d’un **compte Azure**, qui [peut être configuré gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)).
* Vous aurez besoin d’une **instance Azure Digital Twins** dans votre abonnement Azure. Si vous n’avez pas d’instance, vous pouvez en créer une en suivant les étapes décrites du [Tutoriel : Configurer une instance et l’authentification](how-to-set-up-instance-portal.md). Utilisez les valeurs suivantes du programme d’installation pour les utiliser plus loin dans cet article :
    - Nom de l’instance
    - Resource group

Vous trouverez ces informations dans le [portail Azure](https://portal.azure.com) après avoir configuré votre instance. Connectez-vous au portail et recherchez le nom de votre instance dans la barre de recherche du portail.
 
:::image type="content" source="media/how-to-manage-routes-portal/search-field-portal.png" alt-text="Capture d’écran de la barre de recherche du portail Azure." lightbox="media/how-to-manage-routes-portal/search-field-portal.png":::

Sélectionnez votre instance parmi les résultats pour afficher ces détails dans la page Vue d’ensemble de votre instance :

:::image type="content" source="media/how-to-manage-routes-portal/instance-details.png" alt-text="Capture d’écran montrant la page Vue d’ensemble d’une instance Azure Digital Twins. Le nom et le groupe de ressources sont mis en surbrillance.":::

## <a name="create-an-endpoint-for-azure-digital-twins"></a>Créer un point de terminaison pour Azure Digital Twins

Voici les types de points de terminaison pris en charge que vous pouvez créer pour votre instance :
* [Event Grid](../event-grid/overview.md) 
* [Hubs d'événements](../event-hubs/event-hubs-about.md)
* [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)

Pour plus d’informations sur les différents points de terminaison, consultez [Choisir entre les différents services de messagerie Azure](../event-grid/compare-messaging-services.md).

Cette section explique comment créer l’un de ces points de terminaison dans le [portail Azure](https://portal.azure.com).

[!INCLUDE [digital-twins-endpoint-resources.md](../../includes/digital-twins-endpoint-resources.md)]

### <a name="create-the-endpoint"></a>Créer le point de terminaison 

Une fois que vous avez créé les ressources du point de terminaison, vous pouvez les utiliser pour un point de terminaison Azure Digital Twins. Pour créer un point de terminaison, accédez à la page de votre instance dans le [portail Azure](https://portal.azure.com) (vous pouvez rechercher l’instance en entrant son nom dans la barre de recherche du portail).

1. Dans le menu de l’instance, sélectionnez _Points de terminaison_. Ensuite, à partir de la page *Points de terminaison* qui suit, sélectionnez *+ Créer un point de terminaison*. Cette opération ouvre la page *Créer un point de terminaison*, dans laquelle vous allez remplir les champs dans les étapes suivantes.

    :::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-event-grid.png" alt-text="Capture d’écran de la création d’un point de terminaison de type Event Grid." lightbox="media/how-to-manage-routes-portal/create-endpoint-event-grid.png":::

1. Entrez le **nom** de votre point de terminaison, puis choisissez le **type de point de terminaison**.

1. Spécifiez les autres informations requises pour votre type de point de terminaison, y compris votre abonnement et les ressources du point de terminaison décrites [ci-dessus](#prerequisite-create-endpoint-resources).
    1. Pour les points de terminaison Event Hub et Service Bus uniquement, vous devez sélectionner un **type d’authentification**. Vous pouvez utiliser l’authentification basée sur une clé avec une règle d’autorisation pré-créée ou une authentification basée sur l’identité si vous utilisez le point de terminaison avec une [identité managée](concepts-security.md#managed-identity-for-accessing-other-resources-preview) pour votre instance Azure Digital Twins. 

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-event-hub-authentication.png" alt-text="Capture d’écran de la création d’un point de terminaison de type Event Hub." lightbox="media/how-to-manage-routes-portal/create-endpoint-event-hub-authentication.png":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

1. Terminez la création de votre point de terminaison en sélectionnant _Enregistrer_.

>[!IMPORTANT]
> Pour pouvoir utiliser correctement l’authentification basée sur l’identité pour votre point de terminaison, vous devez créer une identité managée pour votre instance en suivant les étapes décrites dans le [Guide pratique : Activer une identité managée pour les événements de routage (préversion)](./how-to-enable-managed-identities-portal.md) .

Après avoir créé votre point de terminaison, vous pouvez le vérifier en sélectionnant l’icône de notification dans la barre supérieure du portail Azure : 

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-notifications.png" alt-text="Capture d’écran de la notification pour vérifier la création du point de terminaison. L’icône en forme de cloche dans la barre supérieure du portail est sélectionnée, et une notification confirme la création du point de terminaison (« Endpoint ADT-eh-endpoint successfully created »).":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Si la création du point de terminaison échoue, observez le message d’erreur et réessayez après quelques minutes.

Vous pouvez également afficher le point de terminaison qui a été créé dans la page *Points de terminaison* de votre instance Azure Digital Twins.

La rubrique Event Grid, Event Hub ou Service Bus est maintenant disponible en tant que point de terminaison dans Azure Digital Twins, sous le nom que vous avez choisi pour le point de terminaison. Vous utiliserez généralement ce nom en tant que cible d’un **itinéraire d’événement** que vous allez créer [plus loin dans cet article](#create-an-event-route).

### <a name="create-an-endpoint-with-dead-lettering"></a>Créer un point de terminaison avec mise en file d’attente de lettres mortes

Lorsqu’un point de terminaison ne peut pas remettre un événement dans un laps de temps donné ou après avoir essayé de remettre l’événement un certain nombre de fois, il peut envoyer l’événement non remis à un compte de stockage. Ce processus est appelé **mise en file d’attente de lettres mortes**.

Pour créer un point de terminaison avec mise en file d’attente de lettres mortes, il est préférable d’utiliser les [commandes CLI](concepts-cli.md) ou les [API de plan de contrôle](/rest/api/digital-twins/controlplane/endpoints/digitaltwinsendpoint_createorupdate) pour créer votre point de terminaison, plutôt que le portail Azure.

Pour obtenir des instructions sur la façon de procéder avec ces outils, consultez la version basée sur [l’interface CLI et les API](how-to-manage-routes-apis-cli.md#create-an-endpoint-with-dead-lettering) de cet article.

## <a name="create-an-event-route"></a>Création d’un itinéraire d’événements

Pour envoyer concrètement des données d’Azure Digital Twins à un point de terminaison, vous devez définir un **itinéraire d’événement**. Ces itinéraires permettent aux développeurs d’associer le flux d’événements au sein du système et aux services en aval. Pour en savoir plus sur les itinéraires d’événements, consultez [Concepts : Routage des événements Azure Digital Twins](concepts-route-events.md).

**Condition préalable** : Vous devez créer des points de terminaison comme décrit précédemment dans cet article avant de pouvoir passer à la création d’un itinéraire. Une fois que vos points de terminaison sont configurés, vous pouvez passer à la création d’un itinéraire d’événements.

>[!NOTE]
>Si vous avez récemment déployé vos points de terminaison, vérifiez que le déploiement est terminé **avant** de tenter de les utiliser pour un nouvel itinéraire d’événements. Si vous ne parvenez pas à configurer l’itinéraire parce que les points de terminaison ne sont pas prêts, patientez quelques minutes, puis réessayez.

### <a name="creation-steps-with-the-azure-portal"></a>Étapes de création avec le portail Azure

Une définition d’itinéraire d’événement contient les éléments suivants :
* nom de l’itinéraire que vous souhaitez utiliser ;
* nom du point de terminaison que vous souhaitez utiliser ;
* filtre définissant les événements envoyés au point de terminaison.
    - Pour désactiver l’itinéraire afin qu’aucun événement ne soit envoyé, utilisez une valeur de filtre `false`.
    - Pour activer un itinéraire qui n’a pas de filtrage spécifique, utilisez une valeur de filtre `true`.
    - Pour plus d’informations sur les autres types de filtre, consultez la section [Filtrer les événements](#filter-events) ci-dessous.

Un seul itinéraire peut permettre la sélection de plusieurs notifications et types d’événements.

Pour créer un itinéraire d’événement, accédez à la page de détails de votre instance Azure Digital Twins dans le [portail Azure](https://portal.azure.com) (vous pouvez rechercher l’instance en entrant son nom dans la barre de recherche du portail).

Dans le menu de l’instance, sélectionnez _Itinéraires d’événements_. Ensuite, à partir de la page *Itinéraires d’événements* qui suit, sélectionnez *+ Créer un itinéraire d’événements*. 

Dans la page *Créer un itinéraire d’événement* qui s’ouvre, choisissez au minimum :
* Un nom pour votre itinéraire dans le champ _Nom_
* Le _point de terminaison_ que vous souhaitez utiliser pour créer la route 

Pour que l’itinéraire soit activé, vous devez également **ajouter un filtre d’itinéraires d’événements** `true` au minimum. (Laisser la valeur `false` par défaut créera l’itinéraire, mais aucun événement ne lui sera envoyé.) Pour ce faire, appuyez sur le bouton bascule de l’_éditeur avancé_ pour l’activer, puis écrivez `true` dans la zone *Filtre*.

:::image type="content" source="media/how-to-manage-routes-portal/create-event-route-no-filter.png" alt-text="Capture d’écran de la création de l’itinéraire d’événement pour votre instance." lightbox="media/how-to-manage-routes-portal/create-event-route-no-filter.png":::

Lorsque vous avez terminé, sélectionnez le bouton _Enregistrer_ pour créer votre itinéraire d’événement.

## <a name="filter-events"></a>Filtrer les événements

Comme décrit ci-dessus, les itinéraires ont un champ **Filtre**. Si la valeur de filtre sur votre itinéraire est `false`, aucun événement n’est envoyé à votre point de terminaison. 

Après avoir activé le filtre minimal `true`, les points de terminaison recevront une variété d’événements d’Azure Digital Twins :
* télémétrie déclenchée par des [jumeaux numériques](concepts-twins-graph.md) à l’aide de l’API de service d’Azure Digital Twins ;
* notifications de changement de propriété de jumeau, déclenchées par des modifications des propriétés d’un jumeau dans l’instance Azure Digital Twins ;
* événements de cycle de vie déclenchés lors de la création ou de la suppression de jumeaux ou de relations ;

Vous pouvez restreindre les types d’événements envoyés en définissant un filtre plus spécifique.

Pour ajouter un filtre d’événements lors de la création d’un itinéraire d’événement, utilisez la section Ajouter un filtre d’itinéraires d’événements de la page *Créer une route d’événement*. 

Vous pouvez sélectionner l’une des options de filtre courantes de base ou utiliser les options de filtre avancées pour écrire vos propres filtres personnalisés.

>[!NOTE]
> Les filtres **respectent la casse** et doivent correspondre à la casse de la charge utile. 
>
> Pour les filtres de télémétrie, cela signifie que la casse doit correspondre à celle de la télémétrie envoyée par l’appareil, pas nécessairement à celle définie dans le modèle du jumeau.

#### <a name="use-the-basic-filters"></a>Utiliser les filtres de base

Pour utiliser les filtres de base, développez l’option _Types d’événements_, puis cochez les cases correspondant aux événements que vous souhaitez envoyer à votre point de terminaison. 

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-event-route-filter-basic-1.png" alt-text="Capture d’écran de la création d’un itinéraire d’événement avec un filtre de base. Sélection des cases à cocher correspondant aux événements.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Cette opération remplit automatiquement la zone de texte de filtre avec le texte du filtre que vous avez sélectionné :

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-event-route-filter-basic-2.png" alt-text="Capture d’écran de la création d’un itinéraire d’événement avec un filtre de base. Indication du texte de filtre rempli automatiquement après la sélection des événements.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

#### <a name="use-the-advanced-filters"></a>Utiliser les filtres avancés

Vous pouvez également utiliser l’option de filtre avancée pour écrire vos propres filtres personnalisés.

Pour créer un itinéraire d’événement avec des options de filtre avancées, appuyez sur le bouton bascule de l’_éditeur avancé_ pour l’activer. Vous pouvez ensuite écrire vos propres filtres d’événement dans la zone *Filtre* :

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-event-route-filter-advanced.png" alt-text="Capture d’écran de la création d’un itinéraire d’événement avec un filtre avancé.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Voici les filtres d’itinéraire pris en charge. Les détails de la colonne *Schéma de texte du filtre* sont le texte qui peut être entré dans la zone de filtre.

[!INCLUDE [digital-twins-route-filters](../../includes/digital-twins-route-filters.md)]

[!INCLUDE [digital-twins-route-metrics](../../includes/digital-twins-route-metrics.md)]

## <a name="next-steps"></a>Étapes suivantes

Apprenez-en davantage sur les différents types de messages d’événements que vous pouvez recevoir :
* [Concepts : Notifications d'événements](concepts-event-notifications.md)