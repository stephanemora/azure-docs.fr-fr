---
title: Architecture de référence d’intégration d’entreprise avec Azure Integration Services
description: Décrit l’architecture de référence qui montre comment implémenter un modèle d’intégration d’entreprise en utilisant Logic Apps, Gestion des API, Service Bus et Event Grid.
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
ms.openlocfilehash: 9eef382ea264bcf9e59dcc408d14a59355b0369b
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/22/2018
ms.locfileid: "42445675"
---
# <a name="reference-architecture-enterprise-integration-with-queues-and-events"></a>Architecture de référence : Intégration d’entreprise avec files d’attente et événements

L’architecture de référence suivante présente un ensemble de pratiques éprouvées que vous pouvez appliquer à une application d’intégration utilisant Azure Integration Services. Cette architecture peut servir de base pour de nombreux modèles d’applications nécessitant des API HTTP, un flux de travail et une orchestration.

![Diagramme de l’architecture – Intégration d’entreprise avec files d’attente et événements](media/logic-apps-architectures-enterprise-integration-with-queues-events/integr_queues_events_arch_diagram.png)

*Il existe de nombreuses applications possibles pour la technologie d’intégration. Elles vont d’une application point à point simple à une application complète Azure Service Bus d’entreprise. Cette gamme d’architectures décrit les composants réutilisables qui peuvent s’appliquer à la création d’une application d’intégration générique. Les architectes doivent prendre en compte les composants qu’ils doivent implémenter pour leur infrastructure et leur application.*

## <a name="architecture"></a>Architecture

Cette architecture *s’appuie* sur l’architecture d’[intégration d’entreprise simple](logic-apps-architectures-simple-enterprise-integration.md). Les [recommandations relatives à l’architecture d’entreprise simple](logic-apps-architectures-simple-enterprise-integration.md#recommendations) s’appliquent ici. Elles sont omises des [recommandations](#recommendations) fournies dans cet article par souci de concision. 

Elle comporte les composants suivants :

- **Groupe de ressources**. Un [groupe de ressources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) est un conteneur logique pour les ressources Azure.
- **Azure API Management** : [Gestion des API](https://docs.microsoft.com/azure/api-management/) est une plateforme intégralement gérée qui sert à publier, sécuriser et transformer les API HTTP.
- **Portail des développeurs Gestion des API Azure**. Chaque instance Gestion des API Azure est fournie avec un accès au [portail des développeurs](https://docs.microsoft.com/azure/api-management/api-management-customize-styles). Le portail des développeurs Gestion des API vous donne accès à la documentation et à des exemples de code. Vous pouvez tester les API dans le portail des développeurs.
- **Azure Logic Apps**. [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview) est une plateforme serverless utilisée pour créer un workflow et une intégration d’entreprise.
- **Connecteurs**. Logic Apps utilise des [connecteurs](https://docs.microsoft.com/azure/connectors/apis-list) pour se connecter aux services couramment utilisés. Logic Apps contient déjà des centaines de connecteurs différents, mais vous pouvez aussi créer un connecteur personnalisé.
- **Azure Service Bus**. [Service Bus](https://docs.microsoft.com/en-us/azure/service-bus-messaging/service-bus-messaging-overview) offre une messagerie sécurisée et fiable, qui peut permettre de découpler des applications et de les intégrer à d’autres systèmes de messagerie.
- **Azure Event Grid**. [Event Grid](https://docs.microsoft.com/azure/event-grid/overview) est une plateforme serverless servant à publier et distribuer des événements d’application.
- **Adresse IP**. Le service Gestion des API Azure a une [adresse IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm) publique fixe et un nom de domaine. Le nom de domaine par défaut est un sous-domaine d’azure-api.net, tel que contoso.azure-api.net, mais les [domaines personnalisés](https://docs.microsoft.com/azure/api-management/configure-custom-domain) peuvent également être configurés. Logic Apps et Service Bus ont également une adresse IP publique. Toutefois, dans cette architecture, nous limitons l’accès pour appeler des points de terminaison Logic Apps uniquement à l’adresse IP de Gestion des API (à des fins de sécurité). Les appels à Service Bus sont sécurisés par une signature d’accès partagé (SAP).
- **Azure DNS**. [Azure DNS](https://docs.microsoft.com/azure/dns/) est un service d’hébergement pour les domaines DNS. Azure DNS offre une résolution de noms à l’aide de l’infrastructure Microsoft Azure. En hébergeant vos domaines dans Azure, vous pouvez gérer vos enregistrements DNS en utilisant les mêmes informations d’identification, les mêmes API, les mêmes outils et la même facturation que pour vos autres services Azure. Pour utiliser un nom de domaine personnalisé (tel que contoso.com), créez des enregistrements DNS qui mappent le nom de domaine personnalisé à l’adresse IP. Pour plus d’informations, consultez [Configurer un nom de domaine personnalisé dans Gestion des API](https://docs.microsoft.com/en-us/azure/api-management/configure-custom-domain).
- **Azure Active Directory (Azure AD)**. Utilisez [Azure AD](https://docs.microsoft.com/azure/active-directory/) ou un autre fournisseur d’identité pour l’authentification. Azure AD fournit l’authentification pour accéder aux points de terminaison des API en transmettant un [jeton web JSON pour la Gestion des API](https://docs.microsoft.com/azure/api-management/policies/authorize-request-based-on-jwt-claims) à valider. Azure AD peut sécuriser l’accès au portail des développeurs Gestion des API (niveaux Standard et Premium uniquement).

Cette architecture présente quelques modèles fondamentaux pour son fonctionnement :

* Les API HTTP principales existantes sont publiées via le portail des développeurs Gestion des API. Dans le portail, les développeurs (internes à votre organisation, externes ou les deux à la fois) peuvent intégrer des appels à ces API dans des applications.
* Les API composites sont générées à l’aide d’applications logiques et en orchestrant des appels à des systèmes SaaS, des services Azure et des API quelconques publiées dans Gestion des API. [Les applications logiques sont également publiées](https://docs.microsoft.com/azure/api-management/import-logic-app-as-api) via le portail des développeurs Gestion des API.
- Les applications utilisent Azure AD pour [acquérir un jeton de sécurité OAuth 2.0](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad) qui est nécessaire pour pouvoir accéder à une API.
- Le service Gestion des API [valide le jeton de sécurité](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad) et transmet la demande à l’application logique ou à l’API principale.
- Les files d’attente Service Bus sont utilisées pour [découpler](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-overview) l’activité d’application et [lisser les pics de charge](https://docs.microsoft.com/azure/architecture/patterns/queue-based-load-leveling). Les messages sont ajoutés aux files d’attente par des applications logiques, par des applications tierces ou en publiant la file d’attente sous la forme d’une API HTTP par le biais de Gestion des API (non représenté).
- Lorsque des messages sont ajoutés à une file d’attente Service Bus, un événement est activé. Une application logique est déclenchée par l’événement. L’application logique traite ensuite le message.
- D’autres services Azure (par exemple, Stockage d'objets blob Azure ou Concentrateurs d'événements Azure) publient également des événements dans Event Grid. Ces services conduisent les applications logiques à recevoir l’événement et à effectuer des actions ultérieures.

## <a name="recommendations"></a>Recommandations

Vos besoins spécifiques peuvent différer de l’architecture générique qui est décrite dans cet article. Utilisez les recommandations de cette section comme point de départ.

### <a name="service-bus-tier"></a>Niveau Service Bus

Utilisez le niveau de Service Bus Premium. Le niveau Premium prend en charge les notifications Event Grid. Pour plus d’informations, consultez [Tarification Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).

### <a name="event-grid-pricing"></a>Prix d’Event Grid

Event Grid utilise un modèle serverless. La facturation est calculée en fonction du nombre d’opérations (exécution d’événement). Pour plus d’informations, consultez [Prix d’Event Grid](https://azure.microsoft.com/pricing/details/event-grid/). Actuellement, aucune considération relative aux niveaux ne s’applique à Event Grid.

### <a name="use-peeklock-to-consume-service-bus-messages"></a>Utiliser PeekLock pour consommer des messages Service Bus

Lorsque vous créez une application logique pour consommer des messages Service Bus, utilisez [PeekLock](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md#queues) dans l’application logique pour accéder à un groupe de messages. Lorsque vous utilisez PeekLock, l’application logique peut suivre des étapes pour valider chaque message avant de terminer ou d’abandonner le message. Cette approche protège contre la perte accidentelle de messages.

### <a name="check-for-multiple-objects-when-an-event-grid-trigger-fires"></a>Chercher les objets multiples lors de l’activation d’un déclencheur Event Grid

Lorsqu’un déclencheur Event Grid est activé, cela signifie simplement « qu’au moins une de ces choses s’est produite ». Par exemple, lorsque Event Grid déclenche une application logique sur un message qui apparaît dans une file d’attente Service Bus, l’application logique doit toujours partir du principe qu’il peut y avoir plusieurs messages à traiter.

### <a name="region"></a>Région

Provisionnez Gestion des API, Logic Apps et Service Bus dans la même région pour réduire au maximum la latence du réseau. Généralement, choisissez la région la plus proche de vos utilisateurs.

Le groupe de ressources a également une région. Cette région indique l’endroit où sont stockées les métadonnées de déploiement et à partir d’où s’exécute le modèle de déploiement. Placez le groupe de ressources et ses ressources dans la même région afin d’améliorer la disponibilité lors du déploiement.

## <a name="scalability"></a>Extensibilité

Le niveau Premium de Service Bus peut augmenter le nombre d’unités de messagerie pour améliorer la scalabilité. Les configurations de niveau Premium peuvent avoir une, deux ou quatre unités de messagerie. Pour plus d’informations sur le dimensionnement de Service Bus, consultez [Bonnes pratiques relatives aux améliorations de performances à l’aide de la messagerie Service Bus](../service-bus-messaging/service-bus-performance-improvements.md).

## <a name="availability"></a>Disponibilité

Actuellement, le contrat de niveau de service (SLA) de la Gestion des API Azure est de 99,9 % pour les niveaux De base, Standard et Premium. Les configurations du niveau Premium avec le déploiement d’au moins une unité dans deux ou plusieurs régions disposent d’un contrat SLA de 99,95 %.

Actuellement, le contrat SLA pour Azure Logic Apps est de 99,9 %.

### <a name="disaster-recovery"></a>Récupération d'urgence

Envisagez d’implémenter la géo-reprise d'activité après sinistre dans Service Bus Premium pour permettre un basculement en cas de panne grave. Pour plus d’informations, consultez [Géo-reprise d'activité après sinistre avec Azure Service Bus](../service-bus-messaging/service-bus-geo-dr.md).

## <a name="manageability"></a>Facilité de gestion

Créez des groupes de ressources distincts pour les environnements de production, de développement et de test. Des groupes de ressources distincts simplifient la gestion des déploiements, la suppression des déploiements de test et l’attribution des droits d’accès.

Lorsque vous attribuez des ressources à des groupes de ressources, considérez les facteurs suivants :

- **Cycle de vie**. D’une façon générale, placez les ressources dotées d’un même cycle de vie dans un même groupe de ressources.
- **Accès**. Vous pouvez utiliser le [contrôle d’accès en fonction du rôle](../role-based-access-control/overview.md) (RBAC) pour appliquer des stratégies d’accès aux ressources d’un groupe.
- **Facturation**. Vous pouvez afficher les coûts cumulés pour le groupe de ressources.
- **Niveau tarifaire pour Gestion des API**. Nous vous recommandons d’utiliser le niveau Développeur pour les environnements de développement et de test. Pour la préproduction, nous vous conseillons de déployer un réplica de votre environnement de production, d’exécuter des tests, puis de l’arrêter afin de réduire les coûts.

Pour plus d’informations, consultez [Présentation d’Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

### <a name="deployment"></a>Déploiement

Nous vous recommandons d’utiliser des [modèles Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) pour déployer Gestion des API, Logic Apps, Event Grid et Service Bus. Les modèles facilitent l’automatisation des déploiements via PowerShell ou l’interface de ligne de commande Azure.

Nous conseillons de placer Gestion des API, ainsi que chacune des applications logiques, chacune des rubriques Event Grid et chacun des espaces de noms Service Bus dans son propre modèle Resource Manager distinct. Lorsque vous utilisez des modèles distincts, vous pouvez stocker les ressources dans les systèmes de contrôle source. Vous pouvez alors déployer ces modèles ensemble ou individuellement dans le cadre d’un processus d’intégration continue/déploiement continu (CI/CD).

### <a name="diagnostics-and-monitoring"></a>Diagnostics et surveillance

Tout comme Gestion des API et Logic Apps, vous pouvez surveiller Service Bus à l’aide d’Azure Monitor. Azure Monitor fournit des informations basées sur les métriques qui sont configurées pour chaque service. Azure Monitor est activé par défaut.

## <a name="security"></a>Sécurité

Sécurisez Service Bus à l’aide d’une SAP. Vous pouvez utiliser l’[authentification SAP](../service-bus-messaging/service-bus-sas.md) pour accorder un accès utilisateur aux ressources Service Bus avec des droits spécifiques. Pour plus d’informations, consultez [Authentification et autorisation Service Bus](../service-bus-messaging/service-bus-authentication-and-authorization.md).

Si une file d’attente Service Bus doit être exposée en tant que point de terminaison HTTP (pour la publication de nouveaux messages), vous devez utiliser Gestion des API pour la sécuriser en exposant le point de terminaison. Le point de terminaison peut alors être sécurisé avec des certificats ou avec OAuth s’il y a lieu. Le moyen le plus simple de sécuriser un point de terminaison consiste à utiliser une application logique avec un déclencheur HTTP de demande/réponse comme intermédiaire.

Event Grid sécurise la distribution des événements au moyen d’un code de validation. Si vous utilisez Logic Apps pour consommer l’événement, la validation est effectuée automatiquement. Pour en savoir plus, consultez la page [Sécurité et authentification pour Event Grid](../event-grid/security-authentication.md).

## <a name="next-steps"></a>Étapes suivantes

- Découvrez l’[intégration d’entreprise simple](logic-apps-architectures-simple-enterprise-integration.md).
