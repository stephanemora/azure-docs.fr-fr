---
title: Architecture de référence d’intégration d’entreprise simple avec Azure Integration Services
description: Décrit l’architecture de référence qui montre comment implémenter un modèle d’intégration d’entreprise simple avec Azure Logic Apps et Gestion des API Azure.
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
ms.openlocfilehash: 982a5eabf8c6c3012a9b3e8fdbe2ff32ba439972
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39113590"
---
# <a name="reference-architecture-simple-enterprise-integration"></a>Architecture de référence : intégration d’entreprise simple

L’architecture de référence suivante présente un ensemble de pratiques éprouvées que vous pouvez appliquer à une application d’intégration utilisant Azure Integration Services. Cette architecture peut servir de base pour de nombreux modèles d’applications nécessitant des API HTTP, un flux de travail et une orchestration.

![Diagramme d’architecture - Intégration d’entreprise simple](./media/logic-apps-architectures-simple-enterprise-integration/simple_arch_diagram.png)

*Il existe de nombreuses applications possibles pour la technologie d’intégration. Elles vont d’une application point à point simple à une application complète Azure Service Bus d’entreprise. Cette gamme d’architectures décrit les composants réutilisables qui peuvent s’appliquer à la création d’une application d’intégration générique. Les architectes doivent prendre en compte les composants qu’ils doivent implémenter pour leur infrastructure et leur application.*

## <a name="architecture"></a>Architecture

Elle comporte les composants suivants :

- **Groupe de ressources**. Un [groupe de ressources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) est un conteneur logique pour les ressources Azure.
- **Azure API Management** : [Gestion des API](https://docs.microsoft.com/azure/api-management/) est une plateforme intégralement gérée qui sert à publier, sécuriser et transformer les API HTTP.
- **Portail des développeurs Gestion des API Azure**. Chaque instance Gestion des API Azure est fournie avec un accès au [portail des développeurs](https://docs.microsoft.com/azure/api-management/api-management-customize-styles). Le portail des développeurs Gestion des API vous donne accès à la documentation et à des exemples de code. Vous pouvez tester les API dans le portail des développeurs.
- **Azure Logic Apps**. [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview) est une plateforme serverless utilisée pour créer un workflow et une intégration d’entreprise.
- **Connecteurs**. Logic Apps utilise des [connecteurs](https://docs.microsoft.com/azure/connectors/apis-list) pour se connecter aux services couramment utilisés. Logic Apps contient déjà des centaines de connecteurs différents, mais vous pouvez aussi créer un connecteur personnalisé.
- **Adresse IP**. Le service Gestion des API Azure a une [adresse IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm) publique fixe et un nom de domaine. Le nom de domaine est un sous-domaine d’azure-api.net, par exemple, contoso.azure-api.net. Logic Apps et Service Bus ont également une adresse IP publique. Toutefois, dans cette architecture, nous limitons l’accès pour appeler des points de terminaison Logic Apps uniquement à l’adresse IP de Gestion des API (à des fins de sécurité). Les appels à Service Bus sont sécurisés par une signature d’accès partagé.
- **Azure DNS**. [Azure DNS](https://docs.microsoft.com/azure/dns/) est un service d’hébergement pour les domaines DNS. Azure DNS offre une résolution de noms à l’aide de l’infrastructure Microsoft Azure. En hébergeant vos domaines dans Azure, vous pouvez gérer vos enregistrements DNS en utilisant les mêmes informations d’identification, les mêmes API, les mêmes outils et la même facturation que pour vos autres services Azure. Pour utiliser un nom de domaine personnalisé tel que contoso.com, créez des enregistrements DNS qui mappent le nom de domaine personnalisé à l’adresse IP. Pour plus d’informations, consultez [Configurer un nom de domaine personnalisé dans Gestion des API](https://docs.microsoft.com/en-us/azure/api-management/configure-custom-domain).
- **Azure Active Directory (Azure AD)**. Utilisez [Azure AD](https://docs.microsoft.com/azure/active-directory/) ou un autre fournisseur d’identité pour l’authentification. Azure AD fournit l’authentification pour accéder aux points de terminaison des API en transmettant un [jeton web JSON pour la Gestion des API](https://docs.microsoft.com/azure/api-management/policies/authorize-request-based-on-jwt-claims) à valider. Azure AD peut sécuriser l’accès au portail des développeurs Gestion des API (niveaux Standard et Premium uniquement).

Cette architecture présente quelques modèles fondamentaux pour son fonctionnement :

- Les API composites sont créées à l’aide d’applications logiques. Elles orchestrent les appels aux systèmes SaaS (logiciel en tant que service), aux services Azure et à toutes les API qui sont publiées dans Gestion des API. [Les applications logiques sont également publiées](https://docs.microsoft.com/azure/api-management/import-logic-app-as-api) via le portail des développeurs Gestion des API.
- Les applications utilisent Azure AD pour [acquérir un jeton de sécurité OAuth 2.0](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad) qui est nécessaire pour pouvoir accéder à une API.
- Le service Gestion des API Azure [valide le jeton de sécurité](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad) et transmet la demande à l’application logique ou l’API principale.

## <a name="recommendations"></a>Recommandations

Vos besoins spécifiques peuvent différer de l’architecture générique décrite dans cet article. Utilisez les recommandations de cette section comme point de départ.

### <a name="azure-api-management-tier"></a>Niveau Gestion des API Azure

Utilisez les niveaux De base, Standard ou Premium de Gestion des API. Ces niveaux offrent un contrat de niveau de service de production et prennent en charge le scale-out (montée en charge) au sein de la région Azure (le nombre d’unités varie selon le niveau). Le niveau Premium prend également en charge le scale-out dans plusieurs régions Azure. Le niveau que vous choisissez doit être basé sur le niveau de débit et l’ensemble de fonctionnalités requis. Pour plus d’informations, consultez la section [Tarification de Gestion des API Azure](https://azure.microsoft.com/pricing/details/api-management/).

Vous êtes facturé pour toutes les instances de Gestion des API lorsqu’elles sont en cours d’exécution. Si vous avez effectué une montée en puissance et que vous n’avez pas besoin de ce niveau de performances tout le temps, envisagez d’utiliser la facturation horaire et la descente en puissance de Gestion des API.

### <a name="logic-apps-pricing"></a>Tarification de Logic Apps

Logic Apps utilise un modèle [serverless](logic-apps-serverless-overview.md). La facturation est calculée en fonction de l’action et de l’exécution du connecteur. Pour plus d’informations, consultez [Tarifs Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/). Actuellement, il n’existe aucune considération de niveau pour Logic Apps.

### <a name="logic-apps-for-asynchronous-api-calls"></a>Logic Apps pour les appels d’API asynchrones

Logic Apps fonctionne mieux dans les scénarios qui ne nécessitent pas une faible latence. Par exemple, il convient le mieux pour les appels d’API asynchrones ou de mi-longueur. Si une latence faible est requise (par exemple, un appel qui bloque une interface utilisateur), nous recommandons l’implémentation de cette API ou de cette opération à l’aide d’une technologie différente. Par exemple, utilisez Azure Functions ou une API web déployée à l’aide d’Azure App Service. Nous recommandons toutefois d’exposer cette API aux consommateurs d’API à l’aide de Gestion des API.

### <a name="region"></a>Région

Provisionnez Gestion des API et Logic Apps dans la même région pour réduire la latence du réseau. Généralement, sélectionnez la région la plus proche de vos utilisateurs.

Le groupe de ressources a également une région. La région indique l’endroit où sont stockées les métadonnées de déploiement et à partir d’où s’exécute le modèle de déploiement. Placez le groupe de ressources et ses ressources dans la même région afin d’améliorer la disponibilité lors du déploiement.

## <a name="scalability"></a>Extensibilité

Les administrateurs de Gestion des API doivent ajouter des [stratégies de mise en cache](../api-management/api-management-howto-cache.md) lorsque cela est nécessaire pour augmenter l’extensibilité du service. La mise en cache permet également de réduire la charge sur les services principaux.

Les niveaux De base, Standard et Premium de Gestion des API Azure peuvent être montés en charge au sein d’une région Azure pour offrir une plus grande capacité. Les administrateurs peuvent utiliser l’option **Métrique de capacité** dans le menu **Métriques** pour analyser l’utilisation de leur service et monter ou descendre en puissance selon le cas.

Recommandations pour la mise à l’échelle d’un service Gestion des API :

- La mise à l’échelle doit tenir compte des modèles de trafic. Les clients avec des modèles de trafic plus volatiles ont plus besoin d’accroître leur capacité.
- Une capacité constante supérieure à 66 % peut indiquer un besoin de monter en puissance.
- Une capacité constante inférieure à 20 % peut indiquer une opportunité de descendre en puissance.
- Il est toujours recommandé d’effectuer un test de charge de votre service Gestion des API avec une charge représentative avant d’activer cette charge en production.

Les services du niveau Premium peuvent être mis à l’échelle dans différentes régions Azure. Les clients qui effectuent un déploiement en mettant à l’échelle les services dans plusieurs régions Azure obtiennent un contrat SLA plus élevé (99,95 % au lieu de 99,9 %) et peuvent configurer les services à proximité des utilisateurs dans plusieurs régions.

Le modèle serverless de Logic Apps signifie que les administrateurs n’ont pas besoin de planifier l’extensibilité des services. Le service s’ajuste automatiquement pour répondre à la demande.

## <a name="availability"></a>Disponibilité

Actuellement, le contrat SLA pour Gestion des API Azure est de 99,9 % pour les niveaux De base, Standard et Premium. Les configurations du niveau Premium avec le déploiement d’au moins une unité dans deux ou plusieurs régions disposent d’un contrat SLA de 99,95 %.

Actuellement, le contrat SLA pour Azure Logic Apps est de 99,9 %.

### <a name="backups"></a>Sauvegardes

Votre configuration de Gestion des API Azure doit être [sauvegardée régulièrement](../api-management/api-management-howto-disaster-recovery-backup-restore.md) (selon la régularité des modifications). Les fichiers de sauvegarde doivent être stockés dans un emplacement ou une région Azure qui diffère de l’emplacement où réside le service. Les clients peuvent alors choisir parmi deux options pour leur stratégie de récupération d’urgence :

- Dans un événement de récupération d’urgence, une nouvelle instance Gestion des API est configurée, la sauvegarde est restaurée dans la nouvelle instance et les enregistrements DNS sont redirigés.
- Les clients conservent une copie passive de leur service dans une autre région Azure (encourant des frais supplémentaires). Les sauvegardes y sont régulièrement restaurées. Dans un événement de récupération d’urgence, seuls les enregistrements DNS doivent être redirigés pour restaurer le service.

Étant donné que les applications logiques peuvent être recréées rapidement et qu’elles sont serverless, elles sont sauvegardées via l’enregistrement d’une copie du modèle Azure Resource Manager associé. Les modèles peuvent être enregistrés dans le contrôle de la source et intégrés dans le cadre d’un processus d’intégration continue/de déploiement continu (CI/CD) d’un client.

Si une application logique qui a été publiée via Gestion des API est déplacée vers un autre centre de données, mettez à jour l’emplacement de l’application. Pour ce faire, utilisez un script PowerShell de base pour mettre à jour la propriété **Backend** de l’API.

## <a name="manageability"></a>Facilité de gestion

Créez des groupes de ressources distincts pour les environnements de production, de développement et de test. L’utilisation de groupes de ressources distincts simplifie la gestion des déploiements, la suppression des déploiements de test et l’attribution des droits d’accès.

Lorsque vous attribuez des ressources à des groupes de ressources, considérez les facteurs suivants :

- **Cycle de vie**. D’une façon générale, placez les ressources dotées d’un même cycle de vie dans un même groupe de ressources.
- **Accès**. Vous pouvez utiliser le [contrôle d’accès en fonction du rôle](../role-based-access-control/overview.md) (RBAC) pour appliquer des stratégies d’accès aux ressources d’un groupe.
- **Facturation**. Vous pouvez afficher les coûts cumulés pour le groupe de ressources.
- **Niveau tarifaire pour Gestion des API**. Nous vous recommandons d’utiliser le niveau Développeur pour les environnements de développement et de test. Pour la préproduction, nous vous conseillons de déployer un réplica de votre environnement de production, d’exécuter des tests, puis de l’arrêter afin de réduire les coûts.

Pour plus d’informations, consultez [Présentation d’Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

### <a name="deployment"></a>Déploiement

Nous vous recommandons d’utiliser des [modèles Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) pour déployer Gestion des API et Logic Apps. Les modèles facilitent l’automatisation des déploiements via PowerShell ou l’interface de ligne de commande Azure.

Nous vous recommandons de placer Gestion des API Azure et toutes les applications logiques individuelles dans leurs propres modèles Resource Manager distincts. Lorsque vous utilisez des modèles distincts, vous pouvez stocker les ressources dans les systèmes de contrôle source. Vous pouvez également déployer les ressources, ensemble ou individuellement, dans le cadre d’un processus de déploiement CI/CD.

### <a name="versions"></a>Versions

Chaque fois que vous modifiez la configuration d’une application logique (ou que vous déployez une mise à jour via un modèle Resource Manager), une copie de cette version est conservée pour des raisons pratiques (toutes les versions qui ont un historique d’exécution sont conservées). Vous pouvez utiliser ces versions pour effectuer le suivi des modifications historiques et promouvoir une version en tant que configuration actuelle de l’application logique. Par exemple, vous pouvez efficacement restaurer une application logique.

La Gestion des API a deux [concepts de contrôle de version](https://blogs.msdn.microsoft.com/apimanagement/2018/01/11/versions-revisions-general-availibility/) distincts (mais complémentaires) :

- Les versions utilisées pour fournir à vos consommateurs d’API le choix de l’API qu’ils peuvent consommer selon leurs besoins (par exemple, v1, v2, bêta ou de production).
- Les révisions qui permettent aux administrateurs d’API d’apporter des modifications à une API en toute sécurité et de les déployer pour les utilisateurs avec des commentaires facultatifs.

Dans le cadre du déploiement, il est judicieux de considérer les révisions de Gestion des API comme un moyen d’apporter des modifications en toute sécurité, de conserver un historique des modifications et de mettre les consommateurs d’API au courant de ces modifications. Une révision peut être créée dans un environnement de développement et déployée entre d’autres environnements à l’aide de modèles Resource Manager.

Même si vous pouvez utiliser les révisions pour tester une API avant d’en faire l’API « actuelle » et de la rendre accessible aux utilisateurs, nous déconseillons l’utilisation de ce mécanisme pour les tests de charge et d’intégration. Utilisez plutôt des environnements de préproduction et de test distincts.

### <a name="configuration"></a>Configuration

N’archivez jamais des mots de passe, des clés d’accès ni des chaînes de connexion dans un contrôle de code source. Si ces valeurs sont nécessaires, utilisez la méthode appropriée pour les déployer et les sécuriser. 

Dans Logic Apps, toutes les valeurs sensibles nécessaires dans l’application logique (qui ne peuvent pas être créées sous la forme d’une connexion) doivent être stockées dans Azure Key Vault et référencées à partir d’un modèle Resource Manager. Nous vous suggérons également d’utiliser des paramètres de modèle de déploiement, ainsi que des fichiers de paramètres pour chaque environnement. Pour plus d’informations, consultez [Sécuriser les paramètres et les entrées dans un flux de travail](logic-apps-securing-a-logic-app.md#secure-parameters-and-inputs-within-a-workflow).

Dans Gestion des API, les secrets sont gérés à l’aide d’objets appelés *valeurs nommées* ou *propriétés nommées*. Ces objets stockent en toute sécurité les valeurs qui sont accessibles dans les stratégies de Gestion des API. Pour plus d’informations, consultez la rubrique sur la [gestion des secrets dans Gestion des API](../api-management/api-management-howto-properties.md).

### <a name="diagnostics-and-monitoring"></a>Diagnostics et surveillance

[Gestion des API](../api-management/api-management-howto-use-azure-monitor.md) et [Logic Apps](logic-apps-monitor-your-logic-apps.md) prennent tous les deux en charge la supervision opérationnelle via [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md). Azure Monitor fournit des informations basées sur les métriques qui sont configurées pour chaque service. Azure Monitor est activé par défaut.

Les options suivantes sont également disponibles pour chaque service :

- Les journaux Logic Apps peuvent être envoyés à [Azure Log Analytics](logic-apps-monitor-your-logic-apps-oms.md) pour une analyse approfondie et la création de tableaux de bord.
- Le service Gestion des API prend en charge la configuration d’Azure Application Insights pour la supervision DevOps.
- Gestion des API prend en charge le [modèle de solution Power BI pour l’analyse personnalisée d’API](http://aka.ms/apimpbi). Les clients peuvent utiliser le modèle de solution pour créer leur propre solution d’analyse personnalisée. Des rapports sont disponibles dans Power BI pour les utilisateurs professionnels.

## <a name="security"></a>Sécurité

Cette section répertorie les considérations relatives à la sécurité qui sont spécifiques aux services Azure décrits dans cet article et qui sont déployés dans l’architecture comme indiqué. Il ne s’agit pas d’une liste complète de bonnes pratiques relatives à la sécurité.

- Contrôle d’accès en fonction du rôle (RBAC) pour garantir des niveaux d’accès appropriés aux utilisateurs.
- Sécurisez les points de terminaison des API publiques dans Gestion des API à l’aide d’OAuth/OpenID Connect. Pour sécuriser les points de terminaison des API publiques, configurez un fournisseur d’identité et ajoutez une stratégie de validation de jeton JSON Web Token (JWT).
- Connectez-vous aux services principaux à partir de Gestion des API en utilisant des certificats mutuels.
- Sécurisez les applications logiques HTTP basées sur des déclencheurs en créant une liste verte d’adresses IP pointant vers l’adresse IP de Gestion des API. Une adresse IP répertoriée sur liste verte empêche d’appeler l’application logique à partir de l’Internet public sans passer d’abord par le service Gestion des API.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez l’[intégration d’entreprise avec files d’attente et événements](logic-apps-architectures-enterprise-integration-with-queues-events.md).
