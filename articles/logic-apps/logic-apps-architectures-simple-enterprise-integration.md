---
title: Azure Integration Services - Intégration Entreprise simple
description: Architecture de référence montrant comment implémenter un modèle d’intégration Entreprise simple avec Azure Logic Apps et la Gestion des API Azure
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
ms.openlocfilehash: 17f591a470bf65d3c9365bca9c5ae2d5a6c9574f
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36231975"
---
# <a name="simple-enterprise-integration---reference-architecture"></a>Intégration Entreprise simple - architecture de référence

## <a name="overview"></a>Vue d'ensemble

Cette architecture de référence présente un ensemble de pratiques éprouvées pour une application d’intégration utilisant Azure Integration Services. Cette architecture peut servir de base pour de nombreux modèles d’applications nécessitant des API HTTP, un flux de travail et une orchestration.

*Il existe de nombreuses applications possibles pour la technologie d’intégration, d’une simple application de point à point à un service bus d’entreprise complet. Cette série sur l’architecture définit les composants réutilisables pour la création d’une application d’intégration : les architectes doivent prendre en compte les composants dont ils auront besoin pour leurs applications et leur infrastructure.*

   ![Diagramme d’architecture - intégration Entreprise simple](./media/logic-apps-architectures-simple-enterprise-integration/simple_arch_diagram.png)

## <a name="architecture"></a>Architecture

Elle comporte les composants suivants :

- Groupe de ressources. Un groupe de ressources est un conteneur logique pour les ressources Azure.
- Gestion des API Azure. La Gestion des API Azure est une plateforme entièrement gérée pour la publication, la sécurisation et la transformation des API HTTP.
- Portail des développeurs Gestion des API Azure. Chaque instance de la Gestion des API Azure est fournie avec un Portail des développeurs, donnant accès à de la documentation, des exemples de code et la possibilité de tester une API.
- Azure Logic Apps. Logic Apps est une plateforme serverless pour la création d’un workflow d’entreprise et l’intégration.
- Connecteurs. Logic Apps utilise des connecteurs pour se connecter aux services couramment utilisés. Logic Apps possède déjà des centaines de connecteurs différents, mais ils peuvent également être créés à l’aide d’un connecteur personnalisé.
- Adresse IP. Le service Gestion des API Azure a une adresse IP publique fixe et un nom de domaine. Le nom de domaine est un sous-domaine d’azure-api.net, comme contoso. azure-api.net. Logic Apps possède également une adresse IP publique. Toutefois, dans cette architecture, nous limitons l’accès pour appeler des points de terminaison Logic Apps uniquement à l’adresse IP de Gestion des API (à des fins de sécurité).
- Azure DNS. Azure DNS est un service d'hébergement pour les domaines DNS et qui offre une résolution de noms à l'aide de l'infrastructure Microsoft Azure. En hébergeant vos domaines dans Azure, vous pouvez gérer vos enregistrements DNS avec les mêmes informations d’identification, les mêmes API, les mêmes outils et la même facturation que vos autres services Azure. Pour utiliser un nom de domaine personnalisé, tel que contoso.com, créez des enregistrements DNS qui mappent le nom de domaine personnalisé sur l’adresse IP. Pour plus d’informations, consultez Configurer un nom de domaine personnalisé dans Gestion des API Azure.
- Azure Active Directory (Azure AD). Utilisez Azure AD ou un autre fournisseur d’identité pour l’authentification. Azure AD fournit l’authentification pour accéder aux points de terminaison de l’API (en transmettant un jeton web JSON pour la Gestion des API à valider) et peut sécuriser l’accès au Portail des développeurs Gestion des API (niveaux Standard et Premium uniquement).

Cette architecture comprend des modèles fondamentaux pour son fonctionnement :

1. Les API HTTP de backend existantes sont publiées via le Portail des développeurs Gestion des API, permettant ainsi aux développeurs (soit en interne dans votre organisation, en externe ou les deux) d’intégrer les appels à ces API dans les applications.
2. Les API composites sont générées à l’aide de Logic Apps et orchestrent les appels aux systèmes SAAS, aux services Azure et aux API publiées dans la Gestion des API. Les Logic Apps sont également publiées via le Portail des développeurs Gestion des API.
3. Les applications acquièrent un jeton de sécurité OAuth 2.0 qui est nécessaire pour accéder à une API à l’aide d’Azure Active Directory.
4. La Gestion des API Azure valide le jeton de sécurité et transmet la requête à l’API backend/Logic App.

## <a name="recommendations"></a>Recommandations

Vos exigences peuvent différer de celles de l’architecture décrite ici. Utilisez les recommandations de cette section comme point de départ.

### <a name="azure-api-management-tier"></a>Niveau Gestion des API Azure

Utilisez les niveaux De base, Standard ou Premium, car ils offrent un contrat de niveau de service de production et prennent en charge le scale-out (montée en charge) au sein de la région Azure (le nombre d’unités varie par niveau). Le niveau Premium prend également en charge le scale-out dans plusieurs régions Azure. Le niveau que vous avez choisi doit être basé sur votre niveau de débit et l’ensemble de fonctionnalité requis. Pour plus d’informations, consultez la section [Tarification de Gestion des API Azure](https://azure.microsoft.com/pricing/details/api-management/).

Vous êtes facturé pour toutes les instances de Gestion des API lorsqu’elles sont en cours d’exécution. Si vous êtes monté en puissance et que vous n’avez pas besoin de ce niveau de performances tout le temps, envisagez d’utiliser la facturation horaire et la descente en puissance de Gestion des API.

### <a name="logic-apps-pricing"></a>Tarification de Logic Apps

Logic Apps fonctionne comme un modèle [serverless](logic-apps-serverless-overview.md) : la facturation est calculée en fonction de l’action et de l’exécution du connecteur. Pour plus d’informations, consultez [Tarification de Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/). Il n’existe actuellement aucune considération de niveau pour Logic Apps.

### <a name="logic-apps-for-asynchronous-api-calls"></a>Logic Apps pour les appels d’API asynchrones

Logic Apps fonctionne mieux dans les scénarios qui ne nécessitent pas une latence faible : par exemple, les appels d’API asynchrones ou à exécution semi-longue. Si une latence faible est requise (par exemple, un appel qui bloque une interface utilisateur), nous recommandons la mise en œuvre de cette API ou opération à l’aide d’une technologie différente, par exemple, Azure Functions ou une API web déployée à l’aide d’App Service. Nous recommandons toutefois que cette API soit exposée à l’aide de Gestion des API aux consommateurs d’API.

### <a name="region"></a>Région

Provisionnez Gestion des API et Logic Apps dans la même région pour réduire la latence du réseau. Généralement, sélectionnez la région la plus proche de vos utilisateurs.

Le groupe de ressources est également doté d’une région, indiquant où les métadonnées de déploiement sont stockées, et à partir d’où le modèle de déploiement est exécuté. Placez le groupe de ressources et ses ressources dans la même région. Cela peut améliorer la disponibilité lors du déploiement.

## <a name="scalability-considerations"></a>Considérations relatives à l’extensibilité

Les administrateurs de Gestion des API doivent ajouter des [stratégies de mise en cache](../api-management/api-management-howto-cache.md) lorsque nécessaire pour augmenter l’évolutivité du service et réduire la charge sur leurs services backend.

Les niveaux De base, Standard et Premium de Gestion des API Azure peuvent être montés en charge au sein d’une région Azure pour offrir une plus grande capacité. Les administrateurs peuvent utiliser les Métriques de capacité dans le menu Métriques pour analyser l’utilisation de leur service et monter ou descendre en puissance selon le cas.

Recommandations pour la mise à l’échelle d’un service Gestion des API :

- La mise à l’échelle doit tenir compte des modèles de trafic. Les clients avec des modèles de trafic plus volatiles auront davantage besoin d’une capacité accrue.
- Une capacité cohérente supérieure à 66 % peut indiquer un besoin de monter en puissance.
- Une capacité cohérente inférieure à 20 % peut indiquer une opportunité de descendre en puissance.
- Il est toujours recommandé de tester le chargement de votre service Gestion des API avec une charge représentative avant de l’activer en production.

Les services du niveau Premium peuvent être mis à l’échelle dans différentes régions Azure. Les clients qui déploient de cette manière bénéficieront d’un contrat de niveau de service plus élevé (99,95 % au lieu de 99,9 %) et peuvent provisionner des services proches des utilisateurs dans plusieurs régions.

Le modèle serverless de Logic Apps signifie que les administrateurs n’ont pas besoin de prendre des précautions supplémentaires pour l’évolutivité du service ; le service se met à l’échelle automatiquement pour répondre à la demande.

## <a name="availability-considerations"></a>Considérations relatives à la disponibilité

Au moment de l’écriture, le contrat de niveau de service (SLA) pour Gestion des API Azure est de 99,9 % pour les niveaux De base, Standard et Premium. Les configurations du niveau Premium avec le déploiement d’au moins une unité dans deux ou plusieurs régions disposent d’un contrat SLA de 99,95 %.

Au moment de l’écriture, le contrat de niveau de service (SLA) pour Azure Logic Apps est 99,9 %.

### <a name="backups"></a>Sauvegardes

La configuration de Gestion des API Azure doit être [sauvegardée régulièrement](../api-management/api-management-howto-disaster-recovery-backup-restore.md) (de manière appropriée selon la régularité des modifications) et les fichiers de sauvegarde stockés dans un emplacement ou une région Azure différent(e) de là où réside le service. Les clients peuvent ensuite choisir l’une de deux options pour leur stratégie de récupération d’urgence :

1. Dans un événement de récupération d’urgence, une nouvelle instance de Gestion des API est provisionnée, la sauvegarde y est restaurée et les enregistrements DNS redirigés.
2. Les clients conservent une copie passive de leur service dans une autre région Azure (encourant des frais supplémentaires). Les sauvegardes y sont régulièrement restaurées. Dans un événement de récupération d’urgence, seuls les enregistrements DNS doivent être redirigés pour restaurer le service.

Étant donné que les applications Logic Apps peuvent être recréées très rapidement et qu’elles sont serverless, elles sont sauvegardées via l’enregistrement d’une copie du modèle Azure Resource Manager associé. Elles peuvent être enregistrées dans le contrôle de la source/intégrées dans le processus d’intégration continue/de déploiement continu (CI/CD) des clients.

L’emplacement des applications Logic Apps qui ont été publiées via Gestion des API doit être mis à jour si celles-ci sont déplacées vers un autre centre de données. Pour ce faire, utilisez un script PowerShell simple pour mettre à jour la propriété Backend de l’API.

## <a name="manageability-considerations"></a>Considérations relatives à la facilité de gestion

Créez des groupes de ressources distincts pour les environnements de production, de développement et de test. Cela simplifie la gestion des déploiements, la suppression des déploiements de test et l’attribution des droits d’accès.
Lorsque vous attribuez des ressources aux groupes de ressources, considérez les points suivants :

- Cycle de vie. D’une façon générale, placez les ressources avec le même cycle de vie dans un même groupe de ressources.
- Accès. Vous pouvez utiliser le [contrôle d’accès en fonction du rôle](../role-based-access-control/overview.md) (RBAC) pour appliquer des stratégies d’accès aux ressources dans un groupe.
- Facturation. Vous pouvez afficher les coûts cumulés pour le groupe de ressources.
- Niveau de tarification pour Gestion des API : nous vous recommandons d’utiliser le niveau Développeur pour les environnements de développement et de test. Pour la préproduction, nous vous recommandons de déployer un réplica de votre environnement de production, d’exécuter des tests, puis de l’arrêter afin de réduire les coûts.

Pour plus d’informations, consultez la présentation du [groupe de ressources](../azure-resource-manager/resource-group-overview.md).

### <a name="deployment"></a>Déploiement

Nous vous recommandons d’utiliser des [modèles Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) pour déployer la Gestion des API Azure et Azure Logic Apps. Les modèles facilitent l’automatisation des déploiements via PowerShell ou l’interface de ligne de commande (CLI) de Azure.

Nous vous recommandons de placer la Gestion des API Azure et toutes les applications Logic Apps individuelles dans leurs propres modèles Resource Manager distincts. Ceci permet de les stocker dans les systèmes de contrôle de la source. Ces modèles peuvent ensuite être déployés ensemble ou individuellement dans le cadre d’un processus de déploiement CI/CD.

### <a name="versions"></a>Versions

À chaque fois que vous modifiez la configuration d’une application Logic Apps (ou que vous déployez une mise à jour via un Resource Manager), une copie de cette version est conservée pour des raisons pratiques (toutes les versions qui ont un historique d’exécution seront conservées). Vous pouvez utiliser ces versions pour effectuer le suivi des modifications historiques et également promouvoir une version en tant que configuration actuelle de l’application Logic Apps. Ainsi, vous pouvez efficacement restaurer une application Logic Apps, par exemple.

La Gestion des API a deux [concepts de contrôle de version](https://blogs.msdn.microsoft.com/apimanagement/2018/01/11/versions-revisions-general-availibility/) distincts (mais complémentaires) :

- Les versions utilisées pour fournir à vos consommateurs d’API le choix de l’API qu’ils peuvent consommer selon leurs besoins (par exemple v1, v2 ou bêta, production).
- Des révisions permettant aux administrateurs d’API d’apporter des modifications à une API en toute sécurité et de les déployer pour les utilisateurs avec le commentaire facultatif.

Dans le cadre du déploiement, les révisions de Gestion des API doivent être considérées comme un moyen d’apporter des modifications en toute sécurité, de conserver un historique des modifications et de mettre les consommateurs d’API au courant de ces modifications. Une révision peut être créée dans un environnement de développement et déployée dans d’autres environnements à l’aide de modèles Resource Manager.

Bien que les révisions puissent être utilisées pour tester une API avant qu’elle ne soit considérée comme « version actuelle » et rendue accessible aux utilisateurs, nous ne recommandons pas l’utilisation de ce mécanisme pour les tests de charge ou d’intégration. À la place, utilisez des environnements de pré-production ou des tests distincts.

### <a name="configuration"></a>Configuration

Ne recherchez jamais des mots de passe, clés d’accès ou chaînes de connexion dans un contrôle de code source. Si elles sont nécessaires, utilisez la méthode appropriée pour déployer et sécuriser ces valeurs. 

Dans Logic Apps, toutes les valeurs sensibles nécessaires au sein de l’application logique (qui ne peuvent pas être créées sous la forme d’une connexion) doivent être stockées dans Azure Key Vault et référencées à partir d’un modèle Resource Manager. Nous vous suggérons également d’utiliser des paramètres de modèle de déploiement, ainsi que des fichiers de paramètres pour chaque environnement. Vous trouverez plus de conseils dans la section relative à la [sécurisation des paramètres et entrées dans un flux de travail](logic-apps-securing-a-logic-app.md#secure-parameters-and-inputs-within-a-workflow).

Dans Gestion des API, les secrets sont gérés à l’aide d’objets appelés Valeurs/Propriétés nommées. Ces derniers stockent en toute sécurité les valeurs qui sont accessibles dans les stratégies de la Gestion des API. Découvrez comment [gérer les secrets dans la Gestion des API](../api-management/api-management-howto-properties.md).

### <a name="diagnostics-and-monitoring"></a>Diagnostics et surveillance

[Gestion des API](../api-management/api-management-howto-use-azure-monitor.md) et [Logic Apps](logic-apps-monitor-your-logic-apps.md) prennent en charge le monitoring opérationnel via [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md). Azure Monitor est activé par défaut et fournit des informations basées sur les différentes métriques configurées pour chaque service.

En outre, il existe davantage d’options pour chaque service :

- Les journaux Logic Apps peuvent être envoyés à [Log Analytics](logic-apps-monitor-your-logic-apps-oms.md) pour une analyse plus approfondie et la création de tableaux de bord.
- Gestion des API prend en charge la configuration d’Application Insights pour le monitoring Dev Ops.
- Gestion des API prend en charge le [modèle de solution Power BI pour l’analytique d’API personnalisée](http://aka.ms/apimpbi). Ce modèle de solution permet aux clients de créer leur propre solution d’analytique personnalisée, avec des rapports disponibles dans Power BI pour les utilisateurs professionnels.

## <a name="security-considerations"></a>Considérations relatives à la sécurité

Cette section répertorie les considérations relatives à la sécurité qui sont spécifiques aux services Azure décrits dans cet article, déployés dans l’architecture comme indiqué. Il ne s’agit pas d’une liste complète de bonnes pratiques relatives à la sécurité.

- Contrôle d’accès en fonction du rôle (RBAC) pour garantir des niveaux d’accès appropriés aux utilisateurs.
- Points de terminaison d’API publics sécurisés dans Gestion des API à l’aide d’OAuth/Open IDConnect. Pour ce faire, configurez un fournisseur d’identité et ajoutez une stratégie de validation JWT.
- Se connecter aux services backend à partir de la Gestion des API à l’aide de certificats mutuels
- Sécurisez les applications Logic Apps HTTP basées sur déclencheur en créant une liste verte d’adresses IP pointant vers l’adresse IP de Gestion des API. Cela empêche l’appel de l’application logique à partir de l’Internet public sans d’abord traverser Gestion des API.

Cette architecture de référence vous a montré comment créer une plateforme d’intégration Entreprise simple à l’aide d’Azure Integration Services.

## <a name="next-steps"></a>Étapes suivantes

* [Intégration Entreprise avec files d’attente et événements](logic-apps-architectures-enterprise-integration-with-queues-events.md)
