---
title: Azure Event Grid sur Kubernetes - Concepts
description: Cet article explique les concepts de base d’Azure Event Grid sur Kubernetes avec Azure Arc (préversion)
author: jfggdl
ms.subservice: kubernetes
ms.author: jafernan
ms.date: 05/25/2021
ms.topic: conceptual
ms.openlocfilehash: e9d7fff98a6d6c062f529528f9f1d3071a038049
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110385776"
---
# <a name="event-grid-on-kubernetes---concepts"></a>Event Grid sur Kubernetes - Concepts
Cet article explique les concepts principaux d’Event Grid sur Kubernetes avec Azure Arc (préversion)

[!INCLUDE [event-grid-preview-feature-note.md](../../../includes/event-grid-preview-feature-note.md)]

## <a name="events"></a>Événements
Un événement est un enregistrement de données qui annonce un fait concernant le fonctionnement d’un système logiciel. En général, un événement annonce un changement d’état en raison d’un signal déclenché par le système ou d’un signal observé par le système. Les événements contiennent deux types d’informations : 

- Des [données d’événement](https://github.com/cloudevents/spec/blob/master/spec.md#event-data) qui représentent l’occurrence d’un changement d’état. 
- Des [attributs de contexte](https://github.com/cloudevents/spec/blob/master/spec.md#context-attributes) qui fournissent des informations contextuelles sur l’occurrence de l’événement.     

    Les données d’événement et les attributs de contexte peuvent être utilisés pour filtrer les événements. 

Event Grid sur Kubernetes prend en charge la spécification de schéma [CloudEvents](https://github.com/cloudevents/spec/tree/master). Voici un exemple d’événement qui utilise le schéma CloudEvents. Event Grid prend en charge une taille d’événement jusqu’à 1 Mo.

```json
[{
       "specVersion": "1.0",
       "type" : "orderCreated",
       "source": "myCompanyName/us/webCommerceChannel/myOnlineCommerceSiteBrandName",
       "id" : "eventId-n",
       "time" : "2020-12-25T20:54:07+00:00",
       "subject" : "account/acct-123224/order/o-123456",
       "dataSchema" : "1.0",
       "data" : {
          "orderId" : "123",
          "orderType" : "PO",
          "reference" : "https://www.myCompanyName.com/orders/123"
      }
}]
```

## <a name="source"></a>Source
L’attribut source décrit le contexte dans lequel un contrôle l’événement s’est produit. La source peut être l’initiateur d’événements. Cependant, dans certains cas, il existe des producteurs qui créent et publient des événements, et ces producteurs sont distincts de la source. Pour des raisons de simplicité, cet article suppose que la source est le producteur des événements. 

Chaque source d’événement produit des événements d’un ou plusieurs types d’événements. En tant que source d’événements, une application définit un ensemble d’événements connexes pour annoncer ses changements d’état. Chaque événement a des informations communes, comme la source de l’événement, l’heure à laquelle l’événement a eu lieu et un identificateur unique. Chaque événement a aussi des informations spécifiques qui concernent seulement le type d’événement spécifique. La prise en charge des événements d’une taille jusqu’à 1 Mo est actuellement en préversion.

Pour connaître les propriétés incluses dans un événement, consultez [Schéma CloudEvents](event-schemas.md#cloudevent-schema).

## <a name="publishers"></a>Serveurs de publication
Les éditeurs d’événements sont des applications ou des systèmes qui envoient des événements à Event Grid pour qu’il les distribue aux abonnés aux événements.

## <a name="topics"></a>Rubriques
Une rubrique est une forme de canal d’entrée qui expose un point de terminaison auquel les éditeurs envoient des événements à Event Grid.

Une rubrique peut être utilisée pour une collection d’événements connexes. Vous pouvez créer une rubrique pour chaque catégorie d’événements connexes. Dans certains cas, la source peut être utilisée pour organiser les événements en catégories, car les sources sont généralement associées à un ensemble de types d’événements étroitement liés (« MyApp.OrderCreated », « MyApp.OderDeleted », « MyApp.OrderRejected », etc.). 

Considérez une application qui envoie des événements liés à la gestion de comptes d’utilisateur et au traitement de commandes. Il est peu probable qu’un gestionnaire d’événements soit destiné à consommer les deux catégories d’événements. Créez deux rubriques personnalisées et laissez les gestionnaires d’événements s’abonner à celle qui les intéresse. Pour les solutions de petite taille, vous pouvez à la place envoyer tous les événements à une seule rubrique. 

## <a name="event-subscribers"></a>Abonnés aux événements
Les éditeurs d’événements sont des systèmes logiciels, comme des microservices, qui exposent des points de terminaison auxquels Event Grid distribue les événements. 

## <a name="event-subscriptions"></a>Abonnements à des événements
Un abonnement à des événements indique à Event Grid quels événements sur une rubrique vous souhaitez recevoir (filtrage des événements) et où les envoyer (routage des événements). Lors de la création d’un abonnement à un événement, vous spécifiez un point de terminaison pour la gestion de l’événement. Vous pouvez sélectionner les événements qui doivent être distribués à votre point de terminaison en configurant des clauses de filtre sur l’abonnement aux événements. 

## <a name="event-handlers"></a>Gestionnaires d’événements
Un gestionnaire d’événements est un système logiciel qui expose un point de terminaison auquel les événements sont envoyés. Le gestionnaire reçoit l’événement et effectue des actions pour le traiter. Event Grid prend en charge plusieurs types de gestionnaires. En tant que gestionnaire, vous pouvez utiliser un service Azure pris en charge hébergé sur Kubernetes ou Azure, ou votre propre solution qui expose un Webhook (point de terminaison), où qu’il soit hébergé. En fonction du type de gestionnaire, Event Grid suit différents mécanismes pour garantir la distribution de l’événement. Si le gestionnaire d’événements de destination est un Webhook HTTP, l’événement fait l’objet d’une nouvelle tentative jusqu’à ce que le gestionnaire retourne le code d’état 200 - OK. Pour plus d’informations, consultez [Gestionnaires d’événements](event-handlers.md).

## <a name="sas-authentication"></a>Authentification avec SAS
Event Grid sur Kubernetes fournit l’authentification basée sur une clé SAS pour la publication d’événements sur des rubriques.

## <a name="event-delivery"></a>Remise d’événement
Event Grid sur Kubernetes fournit un mécanisme fiable de distribution et de nouvelle tentative. Si Event Grid ne peut pas confirmer qu’un événement a été reçu par le point de terminaison du gestionnaire d’événements, il redistribue l’événement. Pour plus d’informations, consultez la page [Distribution et nouvelle tentative de distribution de messages avec Event Grid](delivery-retry.md).

## <a name="batch-event-publishing"></a>Publication d’événements par lot
Quand vous utilisez une rubrique, les événements doivent toujours être publiés dans un tableau. Dans les scénarios avec un débit faible, le tableau ne contient qu’un seul événement. Dans les cas d’utilisation à volume élevé, nous vous recommandons de regrouper plusieurs événements par publication pour augmenter l’efficacité. La taille maximale d’un lot est de 1 Mo. Chaque événement ne doit pas dépasser 1 Mo. Pour plus d’informations, consultez [Distribution d’événements par lot](batch-event-delivery.md).

## <a name="event-grid-on-kubernetes-components"></a>Composants d’Event Grid sur Kubernetes

- L’**opérateur Event Grid** implémente le [modèle Opérateur](https://kubernetes.io/docs/concepts/extend-kubernetes/operator/). Il observe les changements d’état des ressources Event Grid en fonction des demandes de plan de contrôle effectuées au serveur d’API de Kubernetes. Quand une demande affecte l’état d’une ressource Event Grid, l’opérateur Event Grid synchronise cet état avec répartiteur Event Grid.
- Le **répartiteur Event Grid** effectue à la fois les opérations du plan de contrôle et du plan de données.

   En tant que service de plan de contrôle, il est chargé d’amener l’état d’Event Grid à l’état souhaité communiqué par l’opérateur Event Grid. Par exemple, quand il y a une demande de création d’une rubrique, il traite cette demande et les métadonnées du service sont mises à jour.

   En tant que service de plan de données, il traite toutes les demandes de publication d’événements et distribue les événements à leurs destinations configurées sur les abonnements aux événements.

## <a name="next-steps"></a>Étapes suivantes
Pour bien démarrer, consultez [Créer des rubriques et des abonnements](create-topic-subscription.md).