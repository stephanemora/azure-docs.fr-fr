---
title: Modèle d’architecture d’intégration d’entreprise simple - Azure Integration Services
description: Cette référence d’architecture montre comment vous pouvez implémenter un modèle d’intégration d’entreprise simple en utilisant Azure Logic Apps et Gestion des API Azure.
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: mattfarm
ms.author: mattfarm
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 06/15/2018
ms.openlocfilehash: 5ed6fa9f514bae3ea651edba6702714e2680091f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46955943"
---
# <a name="simple-enterprise-integration-architecture"></a>Architecture d’intégration d’entreprise simple

Cet article décrit une architecture d’intégration d’entreprise qui utilise des pratiques éprouvées que vous pouvez appliquer à une application d’intégration quand vous utilisez Azure Integration Services. Vous pouvez utiliser cette architecture comme base pour de nombreux modèles d’applications nécessitant des API HTTP, un flux de travail et une orchestration.

![Diagramme d’architecture - Intégration d’entreprise simple](./media/logic-apps-architectures-simple-enterprise-integration/simple_arch_diagram.png)

Cette gamme décrit les composants réutilisables qui peuvent s’appliquer à la création d’une application d’intégration générique. Étant donné que la technologie d’intégration a de nombreuses applications possibles, allant des applications de point à point simples à des applications Azure Service Bus d’entreprise complètes, prenez en compte les composants que vous devez implémenter pour vos applications et votre infrastructure.

## <a name="architecture-components"></a>Composants de l’architecture

Cette architecture d’intégration d’entreprise inclut ces composants :

- **Groupe de ressources** : un [groupe de ressources](../azure-resource-manager/resource-group-overview.md) est un conteneur logique pour ressources Azure.

- **Gestion des API Azure** : le service [Gestion des API](https://docs.microsoft.com/azure/api-management/) est une plateforme entièrement gérée pour la publication, la sécurisation et la transformation des API HTTP.

- **Portail des développeurs Gestion des API Azure** : chaque instance Gestion des API Azure fournit l’accès au [portail des développeurs](../api-management/api-management-customize-styles.md). Ce portail vous donne accès à la documentation et à des exemples de code. Vous pouvez également tester les API dans le portail des développeurs.

- **Azure Logic Apps** : [Logic Apps](../logic-apps/logic-apps-overview.md) est une plateforme serverless pour la création de workflows et d’intégrations d’entreprise.

- **Connecteurs** : Logic Apps utilise des [connecteurs](../connectors/apis-list.md) pour la connexion aux services couramment utilisés. Logic Apps offre des centaines de connecteurs, mais vous pouvez aussi créer un connecteur personnalisé.

- **adresse IP** : le service Gestion des API Azure a une [adresse IP](../virtual-network/virtual-network-ip-addresses-overview-arm.md) publique fixe et un nom de domaine. Le nom de domaine par défaut est un sous-domaine d’azure-api.net (par exemple, contoso.azure-api.net), mais vous pouvez également configurer des [domaines personnalisés](../api-management/configure-custom-domain.md). Logic Apps et Service Bus ont également une adresse IP publique. Toutefois, à des fins de sécurité, cette architecture limite l’accès pour appeler des points de terminaison Logic Apps uniquement à l’adresse IP de Gestion des API. Les appels à Service Bus sont sécurisés par une signature d’accès partagé (SAP).

- **Azure DNS** : [Azure DNS](https://docs.microsoft.com/azure/dns/) est un service d’hébergement pour les domaines DNS. Azure DNS offre une résolution de noms à l’aide de l’infrastructure Microsoft Azure. En hébergeant vos domaines dans Azure, vous pouvez gérer vos enregistrements DNS en utilisant les mêmes informations d’identification, les mêmes API, les mêmes outils et la même facturation que pour vos autres services Azure. Pour utiliser un nom de domaine personnalisé, tel que contoso.com, créez des enregistrements DNS qui mappent le nom de domaine personnalisé sur l’adresse IP. Pour plus d’informations, consultez [Configurer un nom de domaine personnalisé dans Gestion des API](../api-management/configure-custom-domain.md).

- **Azure Active Directory (Azure AD)**  : vous pouvez utiliser [Azure AD](https://docs.microsoft.com/azure/active-directory/) ou un autre fournisseur d’identité pour l’authentification. Azure AD fournit l’authentification pour accéder aux points de terminaison des API en transmettant un [jeton web JSON pour la Gestion des API](../api-management/policies/authorize-request-based-on-jwt-claims.md) à valider. Pour les niveaux Standard et Premium, Azure AD peut sécuriser l’accès au portail des développeurs Gestion des API.

## <a name="patterns"></a>Modèles 

Cette architecture utilise quelques modèles fondamentaux pour le fonctionnement :

* Les API composites sont générées à l’aide d’applications logiques, qui orchestrent des appels à des systèmes SaaS, des services Azure et des API quelconques publiées dans Gestion des API. Les applications logiques sont également [publiées via le portail des développeurs Gestion des API](../api-management/import-logic-app-as-api.md).

* Les applications utilisent Azure AD pour [acquérir un jeton de sécurité OAuth 2.0](../api-management/api-management-howto-protect-backend-with-aad.md) qui est nécessaire pour pouvoir accéder à une API.

* Le service Gestion des API Azure [valide le jeton de sécurité](../api-management/api-management-howto-protect-backend-with-aad.md) et transmet la demande à l’application logique ou l’API principale.

## <a name="recommendations"></a>Recommandations

Vos besoins spécifiques peuvent différer de l’architecture générique qui est décrite dans cet article. Utilisez les recommandations de cette section comme point de départ.

### <a name="azure-api-management-tier"></a>Niveau Gestion des API Azure

Utilisez les niveaux De base, Standard ou Premium de Gestion des API. Ces niveaux offrent un contrat de niveau de service de production et prennent en charge la montée en charge (scale-out) au sein de la région Azure. Le nombre d’unités varie selon le niveau. Le niveau Premium prend également en charge le scaleout dans plusieurs régions Azure. Choisissez votre niveau en fonction de votre ensemble de fonctionnalités et du niveau de débit requis. Pour plus d’informations, consultez la section [Tarification de Gestion des API Azure](https://azure.microsoft.com/pricing/details/api-management/).

Vous êtes facturé pour toutes les instances de Gestion des API lorsqu’elles sont en cours d’exécution. Si vous avez effectué une montée en puissance et que vous n’avez pas besoin de ce niveau de performances tout le temps, envisagez d’utiliser la facturation horaire et la descente en puissance de Gestion des API.

### <a name="logic-apps-pricing"></a>Tarification de Logic Apps

Logic Apps utilise un modèle [serverless](../logic-apps/logic-apps-serverless-overview.md). La facturation est calculée en fonction de l’action et de l’exécution du connecteur. Pour plus d’informations, consultez [Tarifs Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/). Actuellement, il n’existe aucune considération de niveau pour Logic Apps.

### <a name="logic-apps-for-asynchronous-api-calls"></a>Logic Apps pour les appels d’API asynchrones

Logic Apps fonctionne mieux dans les scénarios qui ne nécessitent pas une faible latence. Par exemple, Logic Apps convient le mieux pour les appels d’API asynchrones ou de mi-longueur. Si une latence faible est requise, par exemple, un appel qui bloque une interface utilisateur, implémentez votre API ou cette opération à l’aide d’une technologie différente. Par exemple, utilisez Azure Functions ou une API web déployée à l’aide d’Azure App Service. Utilisez Gestion des API pour proposer l’API à vos consommateurs d’API.

### <a name="region"></a>Région

Pour réduire au maximum la latence du réseau, choisissez la même région pour Gestion des API, Logic Apps et Service Bus. En général, choisissez la région la plus proche de vos utilisateurs.

Le groupe de ressources a également une région. Cette région indique l’emplacement de stockage des métadonnées de déploiement et l’emplacement d’exécution du modèle de déploiement. Pour améliorer la disponibilité pendant le déploiement, placez le groupe de ressources et les ressources dans la même région.

## <a name="scalability"></a>Extensibilité

Pour augmenter la scalabilité durant l’administration d’un service Gestion des API, ajoutez des [stratégies de mise en cache](../api-management/api-management-howto-cache.md) quand cela est nécessaire. La mise en cache permet également de réduire la charge sur les services principaux.

Pour offrir une plus grande capacité, vous pouvez faire monter en charge les niveaux De base, Standard et Premium de Gestion des API Azure au sein d’une région Azure. Pour analyser l’utilisation de votre service, dans le menu **Métriques**, sélectionnez l’option **Métrique de capacité**, puis effectuez une montée ou descente en puissance selon le cas.

Recommandations pour la mise à l’échelle d’un service Gestion des API :

- Prenez en compte les modèles de trafic pendant une mise à l’échelle. Les clients utilisant des modèles de trafic plus volatiles ont besoin de davantage de capacité.

- Une capacité constante supérieure à 66 % peut indiquer un besoin de monter en puissance.

- Une capacité constante inférieure à 20 % peut indiquer une opportunité de descendre en puissance.

- Avant d’activer la charge en production, effectuez toujours un test de charge de votre service Gestion des API avec une charge représentative.

Vous pouvez mettre à l’échelle les services du niveau Premium dans différentes régions Azure. Si vous effectuez un déploiement en mettant à l’échelle les services dans plusieurs régions Azure, vous pouvez obtenir un contrat SLA plus élevé (99,95 % au lieu de 99,9 %) et provisionner les services à proximité des utilisateurs dans plusieurs régions.

Le modèle serverless de Logic Apps signifie que les administrateurs n’ont pas besoin de planifier la scalabilité des services. Le service s’ajuste automatiquement pour répondre à la demande.

## <a name="availability"></a>Disponibilité

* Pour les niveaux De base, Standard et Premium, le contrat de niveau de service (SLA) de Gestion des API Azure est actuellement de 99,9 %. Pour les configurations du niveau Premium avec un déploiement comportant au moins une unité dans deux ou plusieurs régions, le contrat SLA est de 99,95 %.

* Le contrat SLA pour Azure Logic Apps est actuellement de 99,9 %.

### <a name="backups"></a>Sauvegardes

Selon la régularité des modifications, [sauvegardez régulièrement](../api-management/api-management-howto-disaster-recovery-backup-restore.md) votre configuration de Gestion des API Azure. Stockez vos fichiers de sauvegarde dans un emplacement ou une région Azure qui diffère de l’endroit où réside votre service. Vous pouvez ensuite choisir une des deux options suivantes comme stratégie de reprise d’activité :

* Dans un événement de reprise d’activité, provisionnez une nouvelle instance Gestion des API, restaurez la sauvegarde dans la nouvelle instance et redirigez les enregistrements DNS.

* Conservez une copie passive de votre service dans une autre région Azure, qui encourt des frais supplémentaires. Restaurez les sauvegardes régulièrement dans cette copie. Pour restaurer le service durant un événement de reprise d’activité, vous devez uniquement rediriger les enregistrements DNS.

Étant donné que vous pouvez rapidement recréer les applications logiques, qui sont serverless, sauvegardez-les en enregistrant une copie du modèle Azure Resource Manager associé. Vous pouvez enregistrer les modèles dans le contrôle de code source, et vous pouvez intégrer les modèles à votre processus de déploiement continu/intégration continue (CI/CD).

Si vous avez publié une application logique par le biais de Gestion des API Azure et qu’elle est déplacée vers un autre centre de données, mettez à jour l’emplacement de l’application. Vous pouvez mettre à jour la propriété **back-end** de votre API à l’aide d’un script PowerShell de base.

## <a name="manageability"></a>Facilité de gestion

Créez des groupes de ressources distincts pour les environnements de production, de développement et de test. Des groupes de ressources distincts simplifient la gestion des déploiements, la suppression des déploiements de tests et l’attribution des droits d’accès.

Lorsque vous attribuez des ressources à des groupes de ressources, considérez les facteurs suivants :

* **Cycle de vie** : de façon générale, placez les ressources dotées du même cycle de vie dans le même groupe.

* **Accès** : pour appliquer des stratégies d’accès aux ressources d’un groupe, vous pouvez utiliser le [contrôle d’accès en fonction du rôle (RBAC)](../role-based-access-control/overview.md).

* **Facturation** : vous pouvez afficher les coûts cumulés pour le groupe de ressources.

* **Niveau de tarification pour Gestion des API** : utilisez le niveau Développeur pour vos environnements de développement et de test. Pour réduire les coûts durant la préproduction, déployez un réplica de votre environnement de production, exécutez vos tests, puis arrêtez.

Pour plus d’informations, consultez [Présentation d’Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

## <a name="deployment"></a>Déploiement

* Pour déployer Gestion des API et Logic Apps, utilisez les [modèles Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md). Les modèles facilitent l’automatisation des déploiements à l’aide de PowerShell ou de l’interface de ligne de commande Azure.

* Placez Gestion des API Azure et toutes les applications logiques individuelles dans leurs propres modèles Resource Manager distincts. En utilisant des modèles distincts, vous pouvez stocker les ressources dans les systèmes de contrôle de code source. Vous pouvez alors déployer ces modèles ensemble ou individuellement dans le cadre d’un processus d’intégration continue/déploiement continu (CI/CD).

### <a name="versions"></a>Versions

Chaque fois que vous changez la configuration d’une application logique ou que vous déployez une mise à jour par le biais d’un modèle Resource Manager, Azure conserve une copie de cette version pour des raisons pratiques et conserve toutes les versions qui ont un historique d’exécution. Vous pouvez utiliser ces versions pour effectuer le suivi des modifications historiques ou pour promouvoir une version en tant que configuration actuelle de l’application logique. Par exemple, vous pouvez efficacement restaurer une application logique.

La Gestion des API Azure a les [concepts de contrôle de version](https://blogs.msdn.microsoft.com/apimanagement/2018/01/11/versions-revisions-general-availibility/) distincts, mais complémentaires, suivants :

* Les versions qui offrent à vos consommateurs d’API la possibilité de choisir une version d’API en fonction de leurs besoins, par exemple, v1, v2, bêta ou production

* Les révisions qui permettent aux administrateurs d’API d’apporter des modifications dans une API de manière sécurisée et de les déployer pour les utilisateurs avec des commentaires facultatifs

Pour le déploiement, considérez les révisions Gestion des API comme un moyen d’apporter des modifications de manière sécurisée, de conserver un historique des modifications et de communiquer ces modifications aux consommateurs de votre API. Vous pouvez effectuer une révision dans un environnement de développement et déployer cette modification entre d’autres environnements à l’aide de modèles Resource Manager.

Même si vous pouvez utiliser les révisions pour tester une API avant de rendre ces modifications « actuelles » et accessibles aux utilisateurs, cette méthode est déconseillée pour les tests de charge ou d’intégration. Utilisez plutôt des environnements de préproduction et de test distincts.

### <a name="configuration-and-sensitive-information"></a>Configuration et informations sensibles

Ne recherchez jamais des mots de passe, clés d’accès ou chaînes de connexion dans un contrôle de code source. Si ces valeurs sont nécessaires, sécurisez-les et déployez-les à l’aide des méthodes appropriées. 

Dans Logic Apps, si une application logique nécessite des valeurs sensibles que vous ne pouvez pas créer dans une connexion, stockez ces valeurs dans Azure Key Vault et référencez-les à partir d’un modèle Resource Manager. Utilisez des paramètres de modèle de déploiement, ainsi que des fichiers de paramètres pour chaque environnement. Pour plus d’informations, consultez [Sécuriser les paramètres et les entrées dans un flux de travail](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-and-inputs-within-a-workflow).

Gestion des API gère les secrets à l’aide d’objets appelés *valeurs nommées* ou *propriétés nommées*. Ces objets stockent de manière sécurisée les valeurs auxquelles vous pouvez accéder par le biais des stratégies de Gestion des API. Pour plus d’informations, consultez la rubrique sur la [gestion des secrets dans Gestion des API](../api-management/api-management-howto-properties.md).

## <a name="diagnostics-and-monitoring"></a>Diagnostics et surveillance

Vous pouvez utiliser [Azure Monitor](../azure-monitor/overview.md) pour la supervision opérationnelle dans [Gestion des API](../api-management/api-management-howto-use-azure-monitor.md) et [Logic Apps](../logic-apps/logic-apps-monitor-your-logic-apps.md). Azure Monitor fournit des informations basées sur les métriques configurées pour chaque service, et est activé par défaut.

Chaque service a également ces options :

* Pour une analyse approfondie et la création de tableaux de bord, vous pouvez envoyer les journaux Logic Apps à [Azure Log Analytics](logic-apps-monitor-your-logic-apps-oms.md).

* Pour la supervision DevOps, vous pouvez configurer Azure Application Insights pour le service Gestion des API.

* Gestion des API prend en charge le [modèle de solution Power BI pour l’analyse personnalisée d’API](http://aka.ms/apimpbi). Vous pouvez utiliser ce modèle de solution pour créer votre propre solution d’analytique. Pour les utilisateurs professionnels, Power BI met des rapports à disposition.

## <a name="security"></a>Sécurité

Bien que la liste ci-après ne décrive pas complètement toutes les bonnes pratiques liées à la sécurité, vous y trouverez quelques considérations sur la sécurité qui s’appliquent spécifiquement aux services Azure déployés dans l’architecture qui est décrite dans cet article :

* Pour que les utilisateurs aient des niveaux d’accès appropriés, utilisez le contrôle d’accès en fonction du rôle (RBAC).

* Sécurisez les points de terminaison des API publiques dans Gestion des API à l’aide d’OAuth ou d’OpenID Connect. Pour sécuriser les points de terminaison des API publiques, configurez un fournisseur d’identité et ajoutez une stratégie de validation de jeton JSON Web Token (JWT).

* Connectez-vous aux services principaux à partir de Gestion des API en utilisant des certificats mutuels.

* Sécurisez les applications logiques HTTP basées sur des déclencheurs en créant une liste verte d’adresses IP pointant vers l’adresse IP de Gestion des API. Une adresse IP répertoriée sur liste verte empêche d’appeler l’application logique à partir de l’Internet public sans passer d’abord par le service Gestion des API.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez [l’intégration d’entreprise avec files d’attente et événements](../logic-apps/logic-apps-architectures-enterprise-integration-with-queues-events.md).
