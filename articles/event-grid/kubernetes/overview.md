---
title: Azure Event Grid sur Kubernetes – Vue d’ensemble
description: Cet article fournit une vue d’ensemble d’Event Grid sur Kubernetes avec Azure Arc.
author: jfggdl
ms.author: jafernan
ms.subservice: kubernetes
ms.date: 05/25/2021
ms.topic: overview
ms.openlocfilehash: a958e42f3afab3dccd2a989c9d824a37ed9d9a6c
ms.sourcegitcommit: 9ad20581c9fe2c35339acc34d74d0d9cb38eb9aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110536808"
---
# <a name="event-grid-on-kubernetes-with-azure-arc-preview---overview"></a>Event Grid sur Kubernetes avec Azure Arc (préversion) – Vue d’ensemble
Cet article fournit une vue d’ensemble d’Event Grid sur Kubernetes, de cas d’usage, des fonctionnalités qu’il offre et de sa différence par rapport à Azure Event Grid.

[!INCLUDE [event-grid-preview-feature-note.md](../../../includes/event-grid-preview-feature-note.md)]

## <a name="what-is-event-grid"></a>Qu’est-ce qu’Event Grid ?
Event Grid est un répartiteur d’événements utilisé pour intégrer des charges de travail qui utilisent des architectures pilotées par des événements. Une architecture pilotée par des événements utilise des événements pour communiquer des occurrences dans des changements d’état du système, et constitue une approche d’intégration courante dans des architectures découplées, telles que celles qui utilisent des microservices. Event Grid offre une publication-abonnement (pub-sub), qui est également décrite comme un modèle de communication push-push où des événements sont envoyés (push) à des abonnés qui ne sont pas nécessairement conscients du serveur de publication qui les leur envoie. Ce modèle contraste avec les modèles push-pull classiques, tels que ceux qu’utilisent Azure Service Bus ou Azure Event Hubs, où les clients extraient des messages à partir de courtiers de messages et où, par conséquent, il existe un couplage plus fort entre les courtiers et les clients.

Event Grid est disponible en deux éditions : **Azure Event Grid**, service PaaS complètement managé sur Azure, et Event Grid sur Kubernetes avec Azure Arc, qui vous permet d’utiliser Event Grid sur votre cluster Kubernetes, où qu’il soit déployé, localement ou dans le cloud. 

Par souci de clarté, dans cet article, nous appelons **Event Grid** les fonctionnalités de service générales, quelle que soit l’édition utilisée. Nous appelons **Azure Event Grid** le service managé hébergé sur Azure. Par souci de concision, nous appelons également **Event Grid sur Kubernetes avec Azure Arc** **Event Grid sur Kubernetes**.

Quelle que soit l’édition d’Event Grid que vous utilisez, il existe un **éditeur d’événements** qui envoie des événements à Event Grid, et un ou plusieurs **abonnés aux événements** qui exposent des points de terminaison où ils reçoivent des événements livrés par Event Grid. Les événements publiés sur Event Grid ne doivent pas tous être livrés à tous les abonnés. Event Grid vous permet de sélectionner les événements qui doivent être routés vers une ou plusieurs destinations spécifiques via un ensemble de paramètres de configuration définis dans un **abonnement à des événements**. Vous pouvez utiliser des filtres dans des abonnements à des événements pour router des événements spécifiques vers un point de terminaison ou multidiffuser vers plusieurs points de terminaison. Event Grid offre également un mécanisme de livraison fiable avec une logique de nouvelle tentative. Event Grid est également basé sur des normes ouvertes et prend en charge la [spécification de schéma Cloud Events 1.0](https://github.com/cloudevents/spec/blob/master/spec.md).


## <a name="event-grid-on-kubernetes-with-azure-arc"></a>Event Grid sur Kubernetes avec Azure Arc
Event Grid sur Kubernetes avec Azure Arc est une offre qui vous permet d’exécuter Event Grid sur votre propre cluster Kubernetes. Cette fonctionnalité est activée par l’utilisation de [Kubernetes avec Azure Arc](../../azure-arc/kubernetes/overview.md). Via Kubernetes avec Azure Arc, un [cluster Kubernetes pris en charge](install-k8s-extension.md#supported-kubernetes-distributions) se connecte à Azure. Une fois connecté, vous pouvez [installer Event Grid](install-k8s-extension.md) dessus. 

### <a name="use-case"></a>Cas d’utilisation
Event Grid sur Kubernetes prend en charge différents scénarios d’intégration pilotés par des événements. Toutefois, le scénario global principal pris en charge et exprimé sous la forme d’un récit utilisateur est le suivant :

« En tant que propriétaire d’un système déployé sur un cluster Kubernetes, je souhaite communiquer les changements d’état de mon système en publiant des événements et en configurant le routage de ces événements afin que les gestionnaires d’événements, sous mon contrôle ou autres, puissent traiter les événements de mon système de manière adaptée. »

**Fonctionnalité** qui vous permet de répondre à l’exigence ci-dessus : [Rubriques Event Grid](/rest/api/eventgrid/version2020-10-15-preview/topics).

### <a name="event-grid-on-kubernetes-at-a-glance"></a>Event Grid sur Kubernetes en d’un coup d’œil
Du point de vue de l’utilisateur, Event Grid sur Kubernetes est composé des ressources suivantes en bleu :

:::image type="content" source="./media/overview/event-grid-topics.png" alt-text="Ressources" lightbox="./media/overview/event-grid-topics.png":::

* Une **rubrique** est une sorte de canal d’entrée qui expose un point de terminaison via lequel les éditeurs envoient des événements à Event Grid.
* Un **abonnement à un événement** est une ressource contenant des paramètres de configuration pour filtrer et router des événements vers une destination où les événements sont livrés.
* Un **événement** est l’annonce d’un changement d’état.
* Un **gestionnaire d’événements** est une application ou un service qui reçoit des événements et y réagit ou les traite d’une certaine façon. Parfois, nous appelons également les gestionnaires d’événements **abonnés à des événements**. Dans le diagramme ci-dessus, les gestionnaires d’événements sont l’API déployée sur un cluster Kubernetes (K8s) et le service Azure Service Bus.

Pour plus d’informations sur ces concepts, consultez [Concepts dans Azure Event Grid](concepts.md).

### <a name="sample-workload-integration-scenarios-and-destinations"></a>Exemples de scénarios et de destinations d’intégration de charge de travail

Vous pouvez intégrer des charges de travail s’exécutant sur votre cluster. Votre serveur de publication peut être n’importe quel service s’exécutant sur votre cluster Kubernetes ou toute charge de travail ayant accès au point de terminaison de la rubrique (hébergé par le répartiteur Event Grid) auquel votre serveur de publication envoie des événements.

:::image type="content" source="./media/overview/event-grid-intra-cluster-integration.png" alt-text="Intégration à l’intérieur d’un cluster" lightbox="./media/overview/event-grid-intra-cluster-integration.png":::


Vous pouvez également avoir un serveur de publication déployé ailleurs dans votre réseau, qui envoie des événements à un Event Grid déployé sur l’un de vos clusters Kubernetes :

:::image type="content" source="./media/overview/event-grid-in-network-integration.png" alt-text="Intégration dans un réseau" lightbox="./media/overview/event-grid-in-network-integration.png":::

Avec Event Grid sur Kubernetes, vous pouvez transférer des événements vers Azure en vue d’un traitement, d’un stockage ou d’une visualisation plus poussés :

:::image type="content" source="./media/overview/event-grid-forward-events.png" alt-text="Transfert d’événements vers Azure":::

#### <a name="destinations"></a>Destinations
Les destinations d’un gestionnaire d’événements peuvent être n’importe quel point de terminaison HTTPS ou HTTP auquel Event Grid peut accéder via le réseau, public ou privé, et auquel il a accès (non protégé par un mécanisme d’authentification). Vous définissez des destinations de remise d’événement lorsque vous créez un abonnement à des événements. Pour plus d’informations, consultez la section relative aux [gestionnaires d’événements](event-handlers.md). 

## <a name="features"></a>Fonctionnalités
Event Grid sur Kubernetes prend en charge les [rubriques Event Grid](/rest/api/eventgrid/version2020-10-15-preview/topics), qui sont une fonctionnalité qu’offre également [Azure Event Grid](../custom-topics.md). Les rubriques Event Grid vous aident à réaliser le [cas d’utilisation d’intégration primaire](#use-case) quand vos exigences appellent l’intégration de votre système avec une autre charge de travail que vous possédez ou qui est rendue accessible à votre système.

Voici quelques-unes des fonctionnalités dont vous bénéficiez avec Azure Event Grid sur Kubernetes :

* **[Filtrage d’événement](filter-events.md)**  : filtrez sur le type d’événement, l’objet d’événement ou les données d’événement pour vous assurer que les gestionnaires d’événements ne reçoivent que des événements pertinents.
* **Distribution ramifiée** : abonnez-vous à plusieurs points de terminaison pour le même événement pour envoyer des copies de l’événement à de nombreux emplacements.
* **Basé sur des standards ouverts** : définissez vos événements à l’aide de la [spécification de schéma Cloud Events 1.0](https://github.com/cloudevents/spec/blob/master/spec.md) de CNCF.
* **Fiabilité** : Event Grid présente une logique de nouvelle tentative de livraison d’événement qui permet de s’assurer que les événements atteignent leur destination.

Pour plus d’informations, consultez [fonctionnalités prises en charge par Event Grid sur Kubernetes](features.md).

## <a name="pricing"></a>Tarifs 
Event Grid sur Kubernetes avec Azure Arc est proposé gratuitement pendant la période de préversion.

## <a name="next-steps"></a>Étapes suivantes
Suivez ces étapes dans l’ordre pour commencer à router les événements à l’aide d’Event Grid sur Kubernetes.

1. [Connectez votre cluster à Azure Arc](../../azure-arc/kubernetes/quickstart-connect-cluster.md).
1. [Installez une extension Event Grid](install-k8s-extension.md) qui est la ressource réelle qui déploie Event Grid sur un cluster Kubernetes. Pour en savoir plus sur l’extension, consultez la section [Extension Event Grid](install-k8s-extension.md#event-grid-extension). 
1. [Créez un emplacement personnalisé](../../azure-arc/kubernetes/custom-locations.md). Un emplacement personnalisé représente un espace de noms dans le cluster, et est l’endroit où les rubriques et les abonnements à des événements sont déployés.
1. [Créez une rubrique et un ou plusieurs abonnements à des événements](create-topic-subscription.md).
1. [Publiez des événements](create-topic-subscription.md).

Voici d’autres ressources que vous pouvez utiliser :

* [Kits de développement logiciel (SDK) de plan de données](../sdk-overview.md#data-plane-sdks).
* [Publiez des exemples d’événements à l’aide des kits de développement logiciel (SDK) de plan de données](https://devblogs.microsoft.com/azure-sdk/event-grid-ga/).
* [CLI Event Grid](/cli/azure/eventgrid).
* [Kits de développement logiciel (SDK) de gestion](../sdk-overview.md#management-sdks).