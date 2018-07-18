---
title: Architecture de référence d’Azure Integration Services
description: Architecture de référence montrant comment implémenter un modèle d’intégration entreprise avec Logic Apps, la Gestion des API, Service Bus et Event Grid.
author: mattfarm
manager: jonfan
editor: ''
services: logic-apps api-management
documentationcenter: ''
ms.assetid: ''
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 06/15/2018
ms.author: LADocs; estfan
ms.openlocfilehash: db3350b3c70f6e6f35949e8423334061849b7b37
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36232033"
---
# <a name="enterprise-integration-with-queues-and-events-reference-architecture"></a>Intégration entreprise avec files d’attente et événements : architecture de référence

## <a name="overview"></a>Vue d'ensemble

Cette architecture de référence présente un ensemble de pratiques éprouvées pour une application d’intégration utilisant Azure Integration Services. Elle peut servir de base à de nombreux modèles d’applications nécessitant des API HTTP, un workflow et une orchestration.

*Les technologies d’intégration ont de nombreuses applications possibles, qui vont d’une simple application point à point à un enterprise service bus complet. Cette série sur l’architecture définit les composants réutilisables pour la création de n’importe quelle application d’intégration ; les architectes prendront en compte ceux dont ils auront besoin pour leurs applications et leur infrastructure.*

![Diagramme de l’architecture – Intégration entreprise avec files d’attente et événements](media/logic-apps-architectures-enterprise-integration-with-queues-events/integr_queues_events_arch_diagram.png)

## <a name="architecture"></a>Architecture

Cette architecture s’appuie sur celle qui est décrite dans l’article [Intégration entreprise simple](logic-apps-architectures-simple-enterprise-integration.md). Ses composants sont les suivants :

- Groupe de ressources. Un groupe de ressources est un conteneur logique pour ressources Azure.
- Gestion des API Azure. La Gestion des API Azure est une plateforme entièrement gérée permettant de publier, de sécuriser et de transformer des API HTTP.
- Portail des développeurs Gestion des API Azure. Chaque instance de la Gestion des API Azure s’accompagne d’un Portail des développeurs, qui donne accès à de la documentation, à des exemples de code et à la possibilité de tester une API.
- Azure Logic Apps. Logic Apps est une plateforme serverless permettant de créer une intégration et un workflow d’entreprise.
- Connecteurs. Logic Apps utilise des connecteurs pour se connecter aux services couramment utilisés. Il possède déjà des centaines de connecteurs différents. Cependant, il est également possible de les créer à l’aide d’un connecteur personnalisé.
- Azure Service Bus. Service Bus offre une messagerie sécurisée et fiable, qui peut permettre de découpler des applications les unes des autres et de les intégrer à d’autres systèmes de messagerie.
- Azure Event Grid. Event Grid est une plateforme serverless permettant de publier et de distribuer des événements d’application.
- Adresse IP. Le service Gestion des API Azure a une adresse IP publique fixe et un nom de domaine. Le nom de domaine est un sous-domaine d’azure-api.net, par exemple, contoso.azure-api.net. Logic Apps et Service Bus possèdent également une adresse IP publique ; toutefois, dans cette architecture, nous limitons l’accès aux appels de points de terminaison Logic Apps à l’adresse IP de la Gestion des API (à des fins de sécurité). Les appels à Service Bus sont sécurisés par une signature d’accès partagé.
- Azure DNS. Azure DNS est un service d'hébergement pour les domaines DNS et qui offre une résolution de noms à l'aide de l'infrastructure Microsoft Azure. En hébergeant vos domaines dans Azure, vous pouvez gérer vos enregistrements DNS avec les mêmes informations d’identification, les mêmes API, les mêmes outils et la même facturation que vos autres services Azure. Pour utiliser un nom de domaine personnalisé (par exemple, contoso.com), créez des enregistrements DNS qui mappent le nom de domaine personnalisé avec l’adresse IP. Pour plus d’informations, voir Configurer un nom de domaine personnalisé dans la Gestion des API Azure.
- Azure Active Directory (Azure AD). Utilisez Azure AD ou un autre fournisseur d’identité pour l’authentification. Azure AD assure l’authentification de l’accès aux points de terminaison d’API (en transmettant un jeton JSON Web Token pour que la Gestion des API le valide) et peut sécuriser l’accès au Portail des développeurs Gestion des API (niveaux Standard et Premium uniquement).

Cette architecture suit quelques modèles fondamentaux dans son fonctionnement :

1. Les API HTTP backend existantes sont publiées par le biais du Portail des développeurs Gestion des API, ce qui permet aux développeurs (en interne dans votre organisation, en externe ou les deux) d’intégrer les appels à ces API dans les applications.
2. Les API composites, créées avec Logic Apps, orchestrent les appels aux systèmes SAAS, aux services Azure et à toutes les API publiées dans la Gestion des API. Les applications logiques sont également publiées par le biais du Portail des développeurs Gestion des API.
3. Les applications acquièrent un jeton de sécurité OAuth 2.0, nécessaire pour accéder à une API utilisant Azure Active Directory.
4. La Gestion des API Azure valide le jeton de sécurité et transmet la demande à l’API backend/application logique.
5. Les files d’attente Service Bus sont utilisées pour découpler l’activité d’application et lisser les pics de charge. Les messages sont ajoutés aux files d’attente par Logic Apps, par des applications tierces ou en publiant la file d’attente sous la forme d’une API HTTP par le biais de la Gestion des API (non représenté).
6. Lorsque des messages sont ajoutés à une file d’attente Service Bus, un événement est activé. Une application logique déclenchée par cet événement traite le message.
7. D’autres services Azure (par exemple, le Stockage Blob ou Event Hub) publient également des événements dans Event Grid. Ils conduisent Logic Apps à recevoir l’événement et à effectuer les actions suivantes.

## <a name="recommendations"></a>Recommandations

Vos exigences peuvent différer de celles de l’architecture décrite ici. Utilisez les recommandations de cette section comme point de départ.

### <a name="service-bus-tier"></a>Niveau Service Bus

Utilisez le niveau Premium de Service Bus. En effet, il prend d’ores et déjà en charge les notifications Event Grid (la prise en charge à tous les niveaux est prévue). Voir [Prix de Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).

### <a name="event-grid-pricing"></a>Prix d’Event Grid

Event Grid fonctionne selon un modèle serverless : la facturation est calculée en fonction du nombre d’opérations (exécution de l’événement). Pour plus d’informations, voir [Prix d’Event Grid](https://azure.microsoft.com/pricing/details/event-grid/). Aucune considération relative aux niveaux ne s’applique à Event Grid.

### <a name="use-peeklock-when-consuming-service-bus-messages"></a>Utiliser PeekLock pour consommer des messages Service Bus

Lorsque vous créez des applications logiques pour consommer des messages Service Bus, utilisez [PeekLock](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md#queues) au sein de l’application logique pour accéder à un groupe de messages. L’application logique peut alors valider chaque message étape par étape, jusqu’à la fin ou l’abandon de la procédure. Cette approche protège contre la perte accidentelle de messages.

### <a name="check-for-multiple-objects-when-an-event-grid-trigger-fires"></a>Chercher les objets multiples lors de l’activation d’un déclencheur Event Grid

L’activation d’un déclencheur Event Grid signifie simplement « au moins un de ces éléments s’est produit ». Par exemple, lorsque Event Grid déclenche une application logique sur un message qui apparaît dans une file d’attente Service Bus, l’application logique doit toujours partir du principe qu’il peut y avoir plusieurs messages à traiter.

### <a name="region"></a>Région

Provisionnez Gestion des API, Logic Apps et Service Bus dans la même zone géographique pour réduire le temps de réponse du réseau. Généralement, sélectionnez la région la plus proche de vos utilisateurs.

Le groupe de ressources est également associé à une zone géographique, qui indique l’endroit où sont stockées où les métadonnées de déploiement et celui où s’exécute le modèle de déploiement. Placez le groupe de ressources et ses ressources dans la même région. Cela peut améliorer la disponibilité lors du déploiement.

## <a name="scalability-considerations"></a>Considérations relatives à l’extensibilité

Le niveau Premium Azure Service Bus peut augmenter le nombre d’unités de messagerie pour améliorer l’extensibilité. Il peut comporter 1, 2 ou 4 unités de messagerie. Pour plus d’informations sur la mise à l'échelle d’Azure Service Bus, voir [Meilleures pratiques relatives aux améliorations des performances à l’aide de la messagerie Service Bus](../service-bus-messaging/service-bus-performance-improvements.md).

## <a name="availability-considerations"></a>Considérations relatives à la disponibilité

Au moment de la rédaction de cet article, le contrat de niveau de service (SLA) de la Gestion des API Azure est de 99,9 % pour les niveaux De base, Standard et Premium. Les configurations du niveau Premium avec déploiement d’au moins une unité dans deux régions ou plus disposent d’un contrat SLA de 99,95 %.

Au moment de la rédaction de cet article, le contrat de niveau de service (SLA) d’Azure Logic Apps est de 99,9 %.

### <a name="disaster-recovery"></a>Récupération d'urgence

Envisagez d’implémenter la géo-reprise d'activité après sinistre dans le niveau Premium de Service Bus pour permettre un basculement en cas de panne grave. Pour plus d’informations, voir [Géo-reprise d'activité après sinistre Azure Service Bus](../service-bus-messaging/service-bus-geo-dr.md).

## <a name="manageability-considerations"></a>Considérations relatives à la facilité de gestion

Créez des groupes de ressources distincts pour les environnements de production, de développement et de test. Cela simplifie la gestion des déploiements, la suppression des déploiements de test et l’attribution des droits d’accès.
Lorsque vous attribuez des ressources aux groupes de ressources, considérez les points suivants :

- Cycle de vie. D’une façon générale, placez les ressources avec le même cycle de vie dans un même groupe de ressources.
- Accès. Vous pouvez utiliser le [contrôle d’accès en fonction du rôle](../role-based-access-control/overview.md) (RBAC) pour appliquer des stratégies d’accès aux ressources d’un groupe.
- Facturation. Vous pouvez afficher les coûts cumulés pour le groupe de ressources.
- Niveau tarifaire de la Gestion des API : nous vous recommandons d’utiliser le niveau Développeur pour les environnements de développement et de test. Pour la préproduction, nous vous conseillons de déployer un réplica de votre environnement de production, d’exécuter des tests, puis de l’arrêter afin de réduire les coûts.

Pour plus d’informations, consultez la présentation du [groupe de ressources](../azure-resource-manager/resource-group-overview.md).

### <a name="deployment"></a>Déploiement

Nous vous recommandons d’utiliser des [modèles Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) pour déployer la Gestion des API Azure, Logic Apps, Event Grid et Service Bus. Les modèles facilitent l’automatisation des déploiements effectués avec PowerShell ou l’interface de ligne de commande (CLI) Azure.

Nous conseillons de placer la Gestion des API Azure ainsi que chacune des applications logiques, chacune des rubriques Event Grid et chacun des espaces de noms Service Bus dans son propre modèle Resource Manager. Ce procédé permet de les stocker dans les systèmes de contrôle de code source. Ces modèles pourront ensuite être déployés ensemble ou individuellement dans le cadre d’un processus d’intégration continue/déploiement continu (CI/CD).

### <a name="diagnostics-and-monitoring"></a>Diagnostics et surveillance

Le monitoring de Service Bus, tout comme la Gestion des API et Logic Apps, peut être effectué par Azure Monitor. Activé par défaut, Azure Monitor donne des informations à partir des différentes métriques configurées pour chaque service.

## <a name="security-considerations"></a>Considérations relatives à la sécurité

Sécurisez Service Bus avec une signature d’accès partagé. [L’authentification SAP](../service-bus-messaging/service-bus-sas.md) vous permet d’accorder un accès utilisateur aux ressources Service Bus avec des droits spécifiques. Pour en savoir plus, voir [Authentification et autorisation Service Bus](../service-bus-messaging/service-bus-authentication-and-authorization.md).

En outre, si une file d’attente Service Bus doit être exposée en tant que point de terminaison HTTP (pour permettre la publication de nouveaux messages), il est nécessaire d’utiliser la Gestion des API pour la sécuriser en exposant le point de terminaison. Celui-ci pourra alors être sécurisé avec des certificats ou avec OAuth s’il y a lieu. Le moyen le plus simple pour cela consiste à utiliser une application logique avec un déclencheur de requête/réponse HTTP comme intermédiaire.

Event Grid sécurise la distribution des événements au moyen d’un code de validation. Si vous utilisez Logic Apps pour consommer l’événement, cette opération est effectuée automatiquement. Pour plus d’informations, voir [Sécurité et authentification Event Grid](../event-grid/security-authentication.md).

## <a name="next-steps"></a>Étapes suivantes

* [Intégration entreprise simple](logic-apps-architectures-simple-enterprise-integration.md)
