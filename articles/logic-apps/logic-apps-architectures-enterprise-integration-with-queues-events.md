---
title: Modèle d’architecture d’intégration d’entreprise – Azure Integration Services
description: Cette référence d’architecture vous montre comment implémenter un modèle d’intégration d’entreprise avec Azure Logic Apps, Gestion des API Azure, Azure Service Bus et Azure Event Grid.
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: mattfarm
ms.author: mattfarm
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 06/15/2018
ms.openlocfilehash: 8fbc84b4016659b6d0d6ce9ec47c05a0b241c3d9
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/08/2018
ms.locfileid: "48855372"
---
# <a name="enterprise-integration-architecture-with-queues-and-events"></a>Architecture d’intégration d’entreprise avec files d’attente et événements

Cet article décrit une architecture d’intégration d’entreprise qui utilise des pratiques éprouvées que vous pouvez appliquer à une application d’intégration quand vous utilisez Azure Integration Services. Vous pouvez utiliser cette architecture comme base pour de nombreux modèles d’applications nécessitant des API HTTP, un flux de travail et une orchestration.

![Diagramme de l’architecture – Intégration d’entreprise avec files d’attente et événements](media/logic-apps-architectures-enterprise-integration-with-queues-events/integr_queues_events_arch_diagram.png)

Cette gamme décrit les composants réutilisables qui peuvent s’appliquer à la création d’une application d’intégration générique. Étant donné que la technologie d’intégration a de nombreuses applications possibles, allant des applications de point à point simples à des applications Azure Service Bus d’entreprise complètes, prenez en compte les composants que vous devez implémenter pour vos applications et votre infrastructure.

## <a name="architecture-components"></a>Composants de l’architecture

Cette architecture repose sur celle qui est décrite dans l’article [Architecture de référence : intégration d’entreprise simple](../logic-apps/logic-apps-architectures-simple-enterprise-integration.md). Les [recommandations](../logic-apps/logic-apps-architectures-simple-enterprise-integration.md#recommendations) de cette architecture s’appliquent également ici. Toutefois, par souci de concision, cet article omet ces recommandations de la section [Recommandations](#recommendations). Cette architecture d’intégration d’entreprise inclut les composants suivants :

- **Groupe de ressources** : un [groupe de ressources](../azure-resource-manager/resource-group-overview.md) est un conteneur logique pour ressources Azure.

- **Gestion des API Azure** : le service [Gestion des API](https://docs.microsoft.com/azure/api-management/) est une plateforme entièrement gérée pour la publication, la sécurisation et la transformation des API HTTP.

- **Portail des développeurs Gestion des API Azure** : chaque instance Gestion des API Azure fournit l’accès au [portail des développeurs](../api-management/api-management-customize-styles.md). Ce portail vous donne accès à la documentation et à des exemples de code. Vous pouvez également tester les API dans le portail des développeurs.

- **Azure Logic Apps** : [Logic Apps](../logic-apps/logic-apps-overview.md) est une plateforme serverless pour la création de workflows et d’intégrations d’entreprise.

- **Connecteurs** : Logic Apps utilise des [connecteurs](../connectors/apis-list.md) pour la connexion aux services couramment utilisés. Logic Apps propose des centaines de connecteurs, mais vous pouvez aussi créer un connecteur personnalisé.

- **Azure Service Bus** : [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) offre une messagerie sécurisée et fiable. Vous pouvez l’utiliser pour découpler des applications et les intégrer à d’autres systèmes de messagerie.

- **Azure Event Grid** : [Event Grid](../event-grid/overview.md) est une plateforme serverless permettant de publier et de distribuer des événements d’application.

- **Adresse IP** : le service Gestion des API Azure dispose d’une [adresse IP](../virtual-network/virtual-network-ip-addresses-overview-arm.md) publique fixe et d’un nom de domaine. Le nom de domaine par défaut est un sous-domaine d’azure-api.net (par exemple, contoso.azure-api.net), mais vous pouvez également configurer des [domaines personnalisés](../api-management/configure-custom-domain.md). Logic Apps et Service Bus ont également une adresse IP publique. Toutefois, à des fins de sécurité, cette architecture limite l’accès pour appeler des points de terminaison Logic Apps uniquement à l’adresse IP de Gestion des API. Les appels à Service Bus sont sécurisés par une signature d’accès partagé (SAP).

- **Azure DNS** : [Azure DNS](https://docs.microsoft.com/azure/dns/) est un service d’hébergement pour les domaines DNS. Azure DNS offre une résolution de noms à l’aide de l’infrastructure Microsoft Azure. En hébergeant vos domaines dans Azure, vous pouvez gérer vos enregistrements DNS en utilisant les mêmes informations d’identification, les mêmes API, les mêmes outils et la même facturation que pour vos autres services Azure. Pour utiliser un nom de domaine personnalisé, tel que contoso.com, créez des enregistrements DNS qui mappent le nom de domaine personnalisé sur l’adresse IP. Pour plus d’informations, consultez [Configurer un nom de domaine personnalisé dans Gestion des API](../api-management/configure-custom-domain.md).

- **Azure Active Directory (Azure AD)**  : vous pouvez utiliser [Azure AD](https://docs.microsoft.com/azure/active-directory/) ou un autre fournisseur d’identité pour l’authentification. Azure AD fournit l’authentification pour accéder aux points de terminaison des API en transmettant un [jeton web JSON pour la Gestion des API](../api-management/policies/authorize-request-based-on-jwt-claims.md) à valider. Pour les niveaux Standard et Premium, Azure AD peut sécuriser l’accès au portail des développeurs Gestion des API.

## <a name="patterns"></a>Modèles 

Cette architecture utilise quelques modèles fondamentaux pour le fonctionnement :

* Les API HTTP principales existantes sont publiées via le portail des développeurs Gestion des API. Dans le portail, les développeurs (internes à votre organisation, externes ou les deux)  
peuvent intégrer des appels à ces API dans des applications.

* Les API composites sont générées à l’aide d’applications logiques qui orchestrent des appels à des systèmes SaaS, des services Azure et des API quelconques publiées dans Gestion des API. Les applications logiques sont également [publiées via le portail des développeurs Gestion des API](../api-management/import-logic-app-as-api.md).

* Les applications utilisent Azure AD pour [acquérir un jeton de sécurité OAuth 2.0](../api-management/api-management-howto-protect-backend-with-aad.md) qui est nécessaire pour pouvoir accéder à une API.

* Le service Gestion des API Azure [valide le jeton de sécurité](../api-management/api-management-howto-protect-backend-with-aad.md) et transmet la demande à l’application logique ou l’API principale.

* Les files d’attente Service Bus sont utilisées pour [découpler](../service-bus-messaging/service-bus-messaging-overview.md) l’activité des applications et [lisser les pics de charge](https://docs.microsoft.com/azure/architecture/patterns/queue-based-load-leveling). Les messages sont ajoutés aux files d’attente par des applications logiques, par des applications tierces ou en publiant la file d’attente sous la forme d’une API HTTP par le biais de Gestion des API (non illustré).

* Lorsque des messages sont ajoutés à une file d’attente Service Bus, un événement est activé. L’événement déclenche une application logique, qui traite ensuite le message.

* D’autres services Azure (par exemple, le stockage Blob Azure ou Azure Event Hubs) publient également des événements dans Event Grid. Ces services conduisent les applications logiques à recevoir l’événement et à effectuer des actions ultérieures.

## <a name="recommendations"></a>Recommandations

Vos besoins spécifiques peuvent différer de l’architecture générique qui est décrite dans cet article. Utilisez les recommandations de cette section comme point de départ.

### <a name="service-bus-tier"></a>Niveau Service Bus

Utilisez le niveau Premium de Service Bus, qui prend en charge les notifications Event Grid. Pour plus d’informations, consultez [Tarification Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).

### <a name="event-grid-pricing"></a>Prix d’Event Grid

Event Grid utilise un modèle serverless. La facturation est calculée en fonction du nombre d’opérations (exécutions d’événement). Pour plus d’informations, consultez [Prix d’Event Grid](https://azure.microsoft.com/pricing/details/event-grid/). Actuellement, aucune considération relative aux niveaux ne s’applique à Event Grid.

### <a name="use-peeklock-to-consume-service-bus-messages"></a>Utiliser PeekLock pour consommer des messages Service Bus

Lorsque vous créez une application logique afin de consommer des messages Service Bus, demandez à votre application logique d’utiliser [PeekLock](../service-bus-messaging/service-bus-messaging-overview.md#queues) pour accéder à un groupe de messages. Lorsque vous utilisez PeekLock, l’application logique peut suivre des étapes pour valider chaque message avant de terminer ou d’abandonner le message. Cette approche protège contre la perte accidentelle de messages.

### <a name="check-for-multiple-objects-when-an-event-grid-trigger-fires"></a>Chercher les objets multiples lors de l’activation d’un déclencheur Event Grid

Lorsqu’un déclencheur Event Grid est activé, cette action signifie simplement « qu’au moins un de ces événements s’est produit ». Par exemple, lorsque Event Grid déclenche une application logique sur un message qui apparaît dans une file d’attente Service Bus, l’application logique doit toujours partir du principe qu’il peut y avoir plusieurs messages à traiter.

### <a name="region"></a>Région

Pour réduire au maximum la latence du réseau, choisissez la même région pour Gestion des API, Logic Apps et Service Bus. En général, choisissez la région la plus proche de vos utilisateurs.

Le groupe de ressources a également une région. Cette région indique l’emplacement de stockage des métadonnées de déploiement et l’emplacement d’exécution du modèle de déploiement. Pour améliorer la disponibilité pendant le déploiement, placez le groupe de ressources et les ressources dans la même région.

## <a name="scalability"></a>Extensibilité

Pour bénéficier d’une meilleure extensibilité, le niveau Premium de Service Bus peut augmenter le nombre d’unités de messagerie. Les configurations de niveau Premium peuvent avoir une, deux ou quatre unités de messagerie. Pour plus d’informations sur le dimensionnement de Service Bus, consultez [Bonnes pratiques relatives aux améliorations de performances à l’aide de la messagerie Service Bus](../service-bus-messaging/service-bus-performance-improvements.md).

## <a name="availability"></a>Disponibilité

* Pour les niveaux De base, Standard et Premium, le contrat de niveau de service (SLA) de Gestion des API Azure est actuellement de 99,9 %. Pour les configurations du niveau Premium avec un déploiement comportant au moins une unité dans deux ou plusieurs régions, le contrat SLA est de 99,95 %.

* Le contrat SLA pour Azure Logic Apps est actuellement de 99,9 %.

### <a name="disaster-recovery"></a>Récupération d'urgence

Pour permettre un basculement en cas de panne grave, envisagez d’implémenter la géo-reprise d'activité après sinistre dans Service Bus Premium. Pour plus d’informations, consultez [Géo-reprise d'activité après sinistre avec Azure Service Bus](../service-bus-messaging/service-bus-geo-dr.md).

## <a name="manageability"></a>Facilité de gestion

Créez des groupes de ressources distincts pour les environnements de production, de développement et de test. Des groupes de ressources distincts simplifient la gestion des déploiements, la suppression des déploiements de tests et l’attribution des droits d’accès.

Lorsque vous attribuez des ressources à des groupes de ressources, considérez les facteurs suivants :

* **Cycle de vie** : de façon générale, placez les ressources dotées du même cycle de vie dans le même groupe.

* **Accès** : pour appliquer des stratégies d’accès aux ressources d’un groupe, vous pouvez utiliser le [contrôle d’accès en fonction du rôle (RBAC)](../role-based-access-control/overview.md).

* **Facturation** : vous pouvez afficher les coûts cumulés pour le groupe de ressources.

* **Niveau de tarification pour Gestion des API** : utilisez le niveau Développeur pour vos environnements de développement et de test. Pour réduire les coûts durant la préproduction, déployez un réplica de votre environnement de production, exécutez vos tests, puis arrêtez.

Pour plus d’informations, consultez [Présentation d’Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

## <a name="deployment"></a>Déploiement

* Pour déployer Gestion des API, Logic Apps, Event Grid et Service Bus, utilisez les [modèles Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md). Les modèles facilitent l’automatisation des déploiements à l’aide de PowerShell ou de l’interface de ligne de commande Azure.

* Placez Gestion des API, ainsi que chacune des applications logiques, chacune des rubriques Event Grid et chacun des espaces de noms Service Bus dans leur propre modèle Resource Manager distinct. En utilisant des modèles distincts, vous pouvez stocker les ressources dans les systèmes de contrôle de code source. Vous pouvez alors déployer ces modèles ensemble ou individuellement dans le cadre d’un processus d’intégration continue/déploiement continu (CI/CD).

## <a name="diagnostics-and-monitoring"></a>Diagnostics et surveillance

Tout comme Gestion des API et Logic Apps, vous pouvez surveiller Service Bus à l’aide d’Azure Monitor qui est activé par défaut. Azure Monitor fournit des informations basées sur les métriques qui sont configurées pour chaque service. 

## <a name="security"></a>Sécurité

Pour sécuriser Service Bus, utilisez une signature d’accès partagé. Par exemple, vous pouvez accorder l’accès d’un utilisateur aux ressources Service Bus avec des droits spécifiques à l’aide de l’[authentification SAP](../service-bus-messaging/service-bus-sas.md). Pour plus d’informations, consultez [Authentification et autorisation Service Bus](../service-bus-messaging/service-bus-authentication-and-authorization.md).

Si vous devez exposer une file d’attente Service Bus en tant que point de terminaison HTTP (pour la publication de nouveaux messages, par exemple), utilisez Gestion des API pour la sécuriser en exposant le point de terminaison. Vous pouvez alors sécuriser le point de terminaison avec des certificats ou une authentification OAuth si besoin. Le moyen le plus simple pour sécuriser un point de terminaison consiste à utiliser une application logique avec un déclencheur de requête HTTP/réponse en tant qu’intermédiaire.

Le service Event Grid sécurise la distribution des événements au moyen d’un code de validation. Si vous utilisez Logic Apps pour consommer l’événement, la validation est automatiquement effectuée. Pour en savoir plus, consultez la page [Sécurité et authentification pour Event Grid](../event-grid/security-authentication.md).

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur l’[intégration d’entreprise simple](logic-apps-architectures-simple-enterprise-integration.md).
