---
title: Azure Event Grid sur Kubernetes – Fonctionnalités
description: Cet article compare les fonctionnalités d’Event Grid sur Kubernetes avec Event Grid sur Azure.
author: jfggdl
ms.subservice: kubernetes
ms.author: jafernan
ms.date: 05/25/2021
ms.topic: conceptual
ms.openlocfilehash: 2eea263517d718effc03382e3d8209a9a11f17e7
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/21/2021
ms.locfileid: "112415855"
---
# <a name="event-grid-on-kubernetes-with-azure-arc-features"></a>Fonctionnalités d’Event Grid sur Kubernetes avec Azure Arc
Le service Event Grid sur Kubernetes offre un vaste ensemble de fonctionnalités qui vous aident à intégrer vos charges de travail Kubernetes et à réaliser des architectures hybrides. Il partage les mêmes [API REST](/rest/api/eventgrid/version2020-10-15-preview/topics) (à partir de la version 2020-10-15-preview), [CLI Event Grid](/cli/azure/eventgrid), expérience de portail Azure, [Kits de développement logiciel (SDK) de gestion](../sdk-overview.md#management-sdks) et [Kits de développement logiciel (SDK) de plan de données](../sdk-overview.md#data-plane-sdks) avec Azure Event Grid, l’autre édition du même service. Quand vous êtes prêt à publier des événements, vous pouvez utiliser les [exemples de kit de développement logiciel (SDK) de plan de données fournis dans différentes langages](https://devblogs.microsoft.com/azure-sdk/event-grid-ga/) qui fonctionnent pour les deux éditions d’Event Grid.

Bien qu’Event Grid sur Kubernetes et Azure Event Grid partagent de nombreuses fonctionnalités et que leur objectif soit de fournir la même expérience utilisateur, il existe certaines différences en raison des exigences uniques qu’ils cherchent à satisfaire, et de la phase de leur cycle de vie dans laquelle ils se trouvent. Par exemple, le seul type de rubrique disponible dans Event Grid sur Kubernetes est celui des rubriques Event Grid qui sont parfois également appelées rubriques personnalisées. Les autres types de rubriques (voir ci-dessous) ne sont pas applicables ou ne sont pas encore pris en charge. Les principales différences entre les deux éditions d’Event Grid sont présentées dans le tableau ci-dessous.

[!INCLUDE [event-grid-preview-feature-note.md](../includes/event-grid-preview-feature-note.md)]


## <a name="event-grid-on-kubernetes-vs-event-grid-on-azure"></a>Event Grid sur Kubernetes et Event Grid sur Azure

| Fonctionnalité | Event Grid sur Kubernetes | Azure Event Grid |
|:--|:-:|:-:|
| [Rubriques Event Grid](/rest/api/eventgrid/version2020-10-15-preview/topics) | ✔ | ✔ |
| [Schéma d’événements cloud CNCF](https://github.com/cloudevents/spec/blob/master/spec.md) | ✔ | ✔ |
| Event Grid et schémas personnalisés | ✘* | ✔ |
| Livraison fiable | ✔ | ✔ |
| Mesures  | ✔** | ✔ |
| Azure Monitor  | ✘ | ✔ |
| [Emplacement de lettres mortes](../manage-event-delivery.md#set-dead-letter-location) | ✘ | ✔ |
| [Transférer les événements vers une autre rubrique d’Event Grid](event-handlers.md#azure-event-grid) | ✔ | ✘ |
| [Rubriques système](../system-topics.md) | ✘ | ✔ |
| [Rubriques de domaine](../event-domains.md) | ✘ | ✔ |
| [Événements partenaire](../partner-events-overview.md) | ✘ | ✔ |
| [Validation du point de terminaison de destination](../webhook-event-delivery.md#endpoint-validation-with-event-grid-events) | ✘ | ✔ |
| [Déclencheur Azure Event Grid pour Azure Functions](../../azure-functions/functions-bindings-event-grid-trigger.md) | ✘ | ✔ |
| Connexions hybrides d’Azure Relay en tant que destination | ✘ | ✔ |
| [Filtrage avancé](filter-events.md) | ✔*** | ✔ |
| [Authn/auth de webhook avec AAD](../secure-webhook-delivery.md) | ✘ | ✔ |
| [Livraison d’événements avec identité de ressource](/rest/api/eventgrid/version2020-10-15-preview/eventsubscriptions/createorupdate#deliverywithresourceidentity) | ✘ | ✔ |
| Même ensemble de kits de développement logiciel (SDK) de plan de données | ✔ | ✔ |
| Même ensemble de kits de développement logiciel (SDK) de gestion | ✔ | ✔ |
| Même CLI Event Grid | ✔ | ✔ |

\* Le schéma d’événements cloud 1.0 fournit un mécanisme d’extensibilité et est une norme ouverte. Ces qualités ou fonctionnalités ne sont pas fournies par les schémas d’Event Grid ou personnalisés. Le schéma d’événements cloud 1.0 est une évolution du schéma d’Event Grid.

\** Les métriques pour les rubriques et les abonnements aux événements sont fournies à l’aide du [format d’exposition Prometheus](https://prometheus.io/docs/instrumenting/exposition_formats/). Les métriques ou d’autres fonctionnalités de surveillance sur le portail Azure ne sont pas disponibles actuellement dans la préversion.

\*** Event Grid sur Kubernetes prend en charge le filtrage avancé d’événements basé sur des valeurs dans les données d’événement, comme le fait Event Grid sur Azure, mais il existe quelques fonctionnalités et opérateurs qu’Event Grid sur Kubernetes ne prend pas en charge. Pour plus d’informations, consultez [Filtrage avancé](filter-events.md#filter-by-values-in-event-data).

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur Event Grid sur Kubernetes, consultez [Event Grid sur Kubernetes avec Azure Arc (préversion) – Vue d’ensemble](overview.md).