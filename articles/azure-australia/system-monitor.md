---
title: Surveillance du système pour la sécurité dans la région Azure Australie
description: Conseils sur la configuration de System Monitoring dans les régions situées en Australie pour satisfaire aux exigences spécifiques des réglementations, de la législation et de la politique du secteur public.
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: f7f78dbde9810c8786e2344555444efabcc989b0
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779321"
---
# <a name="system-monitoring-for-security-in-azure-australia"></a>Surveillance du système pour la sécurité dans la région Azure Australie

Le fait d’avoir des stratégies de sécurité robustes qui incluent la surveillance en temps réel et des évaluations de sécurité de routine est essentiel pour améliorer la sécurité opérationnelle quotidienne de vos environnements informatiques, notamment le cloud.

La sécurité du cloud est un effort commun entre le client et le fournisseur de cloud. Microsoft Azure fournit 4 services pour répondre à ces exigences en tenant compte des recommandations contenues dans [Australian Cyber Security Centre's (ACSC) Information Security Manual Controls](https://acsc.gov.au/infosec/ism/index.htm) (ISM), en particulier l’implémentation de la journalisation centralisée des événements, l’audit des journaux des événements et la gestion et l’évaluation des vulnérabilités de sécurité. Les services Microsoft Azure sont :

* Azure Security Center
* Azure Monitor
* Azure Advisor
* Azure Policy

ACSC recommande d’utiliser ces services pour les données **protégées**. À l’aide de ces services, vous pouvez surveiller et analyser vos environnements informatiques de manière proactive et prendre des décisions avisées sur l’emplacement où allouer des ressources pour améliorer votre sécurité. Chacun de ces services fait partie d’une solution combinée pour vous fournir les meilleures connaissances, recommandations et protection possibles.

## <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) fournit une console de gestion de la sécurité unifiée qui vous permet de surveiller et d’améliorer la sécurité des ressources Azure et de vos données hébergées. Azure Security Center inclut un degré de sécurisation, score basé sur une analyse de l’état de la configuration des meilleures pratiques à partir d’Azure Advisor et la conformité générale d’Azure Policy.

Azure Security Center fournit aux clients Azure les fonctionnalités suivantes :

* Stratégie de sécurité, évaluation et recommandations
* Recherche et collecte des événements de sécurité
* Contrôles des applications et des accès
* Détection avancée des menaces
* Contrôle d’accès juste-à-temps sur les machines virtuelles
* Sécurité hybride

L’étendue des ressources surveillées par Azure Security Center peut être agrandie pour inclure les ressources locales prises en charge dans un environnement cloud hybride. Cela inclut les ressources locales actuellement surveillées par une version prise en charge de System Center Operations Manager.

Le niveau « Standard » de Security Center fournit également des contrôles de sécurité basés sur le cloud requis par [ASD Essential 8](https://acsc.gov.au/publications/protect/essential-eight-explained.htm). Ceux-ci incluent la liste verte d’applications et la restriction des privilèges d’administration via l’accès juste-à-temps.

### <a name="azure-monitor"></a>Azure Monitor

[Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) est la solution de journalisation centralisée pour toutes les ressources Azure, et inclut Log Analytics et Application Insights. Deux types de données clés sont collectés à partir des ressources Azure : les journaux et les métriques. Une fois collectées dans Azure Monitor, les informations de journalisation peuvent être utilisées par un large éventail d’outils et à différentes fins.

![Vue d’ensemble d’Azure Monitor](media/overview.png)

Azure Monitor inclut également le journal d’activité Azure. Le journal d’activité SActivity stocke tous les événements au niveau abonnement qui se sont produits dans Azure. Il permet aux clients Azure de voir les circonstances (qui, quoi et quand) des opérations effectuées sur leurs ressources Azure. La journalisation basée sur les ressources envoyée à Azure Monitor et les événements de journal d’activité Azure peuvent être analysés à l’aide du langage de requête Kusto intégré. Ces journaux peuvent ensuite être exportés, utilisés pour créer des vues et des tableaux de bord personnalisés, et configurés pour déclencher des alertes et des notifications.

### <a name="azure-advisor"></a>Azure Advisor

[Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-overview) analyse les ressources Azure prises en charge, les fichiers journaux générés par le système et les configurations de ressources actuelles au sein de votre abonnement Azure. Les analyses fournies dans Azure Advisor sont générées en temps réel et basées sur les meilleures pratiques recommandées par Microsoft. Toutes les ressources Azure prises en charge ajoutées à votre environnement sont analysées et des recommandations appropriées sont fournies. Les recommandations Azure Advisor sont classées en quatre catégories de meilleures pratiques :

* Sécurité
* Haute disponibilité
* Performances
* Coût

Les recommandations de sécurité générées par Azure Advisor font partie de l’analyse de sécurité globale fournie par Azure Security Center.

Les informations collectées par Azure Advisor fournissent aux administrateurs les éléments suivants :

* Aperçu de la configuration des ressources qui ne répond pas aux meilleures pratiques recommandées
* Aide sur des actions de correction spécifiques à entreprendre
* Classements indiquant les actions correctives à entreprendre avec une priorité élevée

### <a name="azure-policy"></a>Azure Policy

[Azure Policy](https://docs.microsoft.com/azure/governance/policy/overview) offre la possibilité d’appliquer des règles qui régissent les types de ressources Azure et leur configuration autorisée. La stratégie peut être utilisée pour contrôler la création et la configuration des ressources, ou pour auditer des paramètres de configuration dans un environnement. Ces résultats d’audit peuvent être utilisés pour former la base des activités de correction. Azure Policy diffère du contrôle d’accès en fonction du rôle (RBAC) et est utilisé pour restreindre les ressources et leur configuration. RBAC est utilisé pour restreindre l’accès privilégié aux utilisateurs Azure.

Que la stratégie spécifique soit appliquée ou que l’effet de la stratégie soit audité, la conformité de la stratégie est surveillée en permanence et des informations de conformité globales et propres aux ressources sont fournies aux administrateurs. Les données de conformité Azure Policy sont fournies pour Azure Security Center et font partie du degré de sécurisation.

## <a name="key-design-considerations"></a>Considérations clés en matière de conception

Lors de l’implémentation d’une stratégie de journal des événements, ACSC ISM met en évidence les points suivants :

* Fonctionnalités de journalisation centralisée
* Événements spécifiques à journaliser
* Protection des journaux des événements
* Rétention des journaux des événements
* Audit des journaux des événements

En plus de la collecte et de la gestion des journaux, ISM recommande également l’évaluation de la vulnérabilité de routine de l’environnement informatique d’une organisation.

### <a name="centralised-logging"></a>Journalisation centralisée

Chaque solution de journalisation doit, dans la mesure du possible, consolider les journaux capturés dans un référentiel de données unique. Cela permet non seulement de réduire la complexité opérationnelle et d’empêcher la création de plusieurs silos de données, mais aussi d’analyser les données collectées à partir de plusieurs sources, et ainsi d’identifier les événements corrélés. Cela est essentiel pour la détection et la gestion de l’étendue des incidents de sécurité informatiques.

Cette condition est remplie pour tous les clients Azure avec Azure Monitor. Cette offre fournit non seulement un référentiel de journalisation centralisé dans Azure pour toutes les ressources Azure, mais vous permet également de diffuser vos données Azure Event Hub. Azure Event Hubs est un service d’ingestion de données en temps réel complètement managé. Une fois que les données Azure Monitor ont été transmises à Azure Event Hub, elles peuvent également être facilement connectées à des référentiels SIEM (Security information and Event Management) pris en charge et à des outils de surveillance tiers supplémentaires.

Microsoft propose également sa propre solution SIEM Native Azure nommée Azure Sentinel. Azure Sentinel prend en charge un large éventail de connecteurs de données et peut être utilisé pour surveiller les événements de sécurité au sein d’une entreprise. En combinant les données à partir des [connecteurs de données](https://docs.microsoft.com/azure/sentinel/connect-data-sources) pris en charge, les fonctionnalités Machine Learning intégrées d’Azure Sentinel et le langage de requête Kusto, les administrateurs de la sécurité disposent d’une solution unique pour la détection des alertes, la visibilité des menaces, la chasse proactive et la réponse aux menaces. Azure Sentinel fournit également une fonctionnalité de chasse et de bloc-notes qui permet aux administrateurs de sécurité d’enregistrer toutes les étapes effectuées dans le cadre d’une investigation de sécurité dans un playbook réutilisable pouvant être partagé au sein d’une organisation. Les administrateurs de la sécurité peuvent même utiliser l’[analytique utilisateur](https://docs.microsoft.com/azure/sentinel/user-analytics) intégrée pour examiner les actions d’un seul utilisateur désigné.

### <a name="logged-events-and-log-detail"></a>Détails des journaux et des événements journalisés

ISM fournit une liste détaillée des types de journaux d’événements qui doivent être inclus dans toute stratégie de journalisation. Tous les journaux capturés doivent contenir suffisamment de détails pour être utilisés de manière pratique lors de l’analyse et de l’investigation.

Les journaux collectés dans Azure relèvent de l’une des trois catégories suivantes :

* **Journaux d’activité de contrôle et gestion** : ces journaux fournissent des informations sur les opérations CREATE, UPDATE et DELETE Azure Resource Manager.

* **Journaux d’activité de plan de données** : ceux-ci contiennent des événements déclenchés dans le cadre de l’utilisation des ressources Azure. Cela inclut des sources telles que les journaux des événements Windows, notamment les journaux d’activité système, de sécurité et d’application.

* **Événements traités** : ces événements contiennent des informations sur les événements et les alertes qui ont été traités automatiquement au nom du client par Azure. Les alertes Azure Security Center sont un exemple d’événement traité.

L’analyse des machines virtuelles Azure est améliorée par le déploiement de l’agent de machine virtuelle pour Windows et Linux. Cela augmente de façon importante l’ampleur des informations de journalisation collectées. Le déploiement de cet agent peut être configuré pour s’exécuter automatiquement via Azure Security Center.

Microsoft fournit des informations détaillées sur les journaux spécifiques aux ressources Azure et leurs [schémas](https://docs.microsoft.com/azure/security/fundamentals/log-audit).

### <a name="log-retention-and-protection"></a>Rétention et protection des journaux

 Les journaux des événements doivent être stockés en toute sécurité pendant la période de rétention requise. ISM recommande que les journaux soient conservés pendant au moins 7 ans. Azure fournit un certain nombre de moyens pour garantir la durée de vie des journaux collectés. Par défaut, les événements du journal d’activité Azure sont conservés pendant 90 jours. Les données de journal capturées par Azure Monitor peuvent être déplacées et stockées dans un compte de stockage Azure, comme requis pour la rétention à long terme. Les journaux d’activité stockés sur un compte de stockage Azure peuvent être conservés pendant un nombre défini de jours ou indéfiniment si nécessaire.

Les comptes de stockage Azure utilisés pour stocker les événements de journal Azure peuvent être rendus géoredondants et peuvent être sauvegardés à l’aide de Sauvegarde Azure. Une fois la capture effectuée par Sauvegarde Azure, la suppression de sauvegardes contenant des journaux nécessite une approbation de la part de l’administrateur et les sauvegardes marquées pour suppression sont conservées pendant 14 jours, ce qui permet la récupération. Sauvegarde Azure autorise 9 999 copies d’une instance protégée, en fournissant plus de 27 ans de sauvegardes quotidiennes.

Le contrôle d’accès en fonction du rôle doit être utilisé pour contrôler l’accès aux ressources utilisées pour la journalisation Azure. Azure Monitor, les comptes de stockage Azure et les sauvegardes Azure doivent être configurés avec des contrôles d’accès en fonction du rôle pour garantir la sécurité des données contenues dans les journaux.

### <a name="log-auditing"></a>Audit des journaux

La valeur réelle des journaux est réalisée une fois qu’ils sont analysés. En utilisant l’analyse automatisée et manuelle et en vous familiarisant avec les outils disponibles, vous pouvez détecter et gérer les violations de la stratégie de sécurité organisationnelle et les incidents de sécurité informatiques. Azure Monitor fournit un ensemble complet d’outils pour analyser les journaux collectés. Le résultat de cette analyse peut ensuite être partagé entre des systèmes, visualisé ou diffusé dans plusieurs formats.

Les données de journal stockées dans Azure Monitor sont conservées dans un espace de travail Log Analytics. Toutes les analyses commencent par une requête. Les requêtes Azure Monitor sont écrites dans le langage de requête Kusto. Les requêtes constituent la base de toutes les sorties Azure Monitor, des tableaux de bord Azure aux règles d’alerte.

![Présentation des requêtes de journal Azure](media/queries-overview.png)

L’audit des journaux peut être amélioré grâce à l’utilisation de solutions de supervision. Il s’agit de solutions préconfigurées qui contiennent une logique de collecte, des requêtes et des vues de visualisation de données. Microsoft [fournit](https://docs.microsoft.com/azure/azure-monitor/insights/solutions-inventory) un certain nombre de solutions de supervision et des solutions supplémentaires sont proposées par les fournisseurs de produits sur la Place de marché Azure.

### <a name="vulnerability-assessment-and-management"></a>Gestion et évaluation des vulnérabilités

ISM note que la gestion et l’évaluation des vulnérabilités de routine sont essentielles. Votre environnement informatique évolue constamment et les menaces de sécurité externes évoluent de façon infinie. Avec Azure Security Center, vous pouvez effectuer des évaluations de vulnérabilité automatisées et obtenir des conseils sur la façon de planifier et d’effectuer des activités de correction.

Le degré de sécurisation dans Azure Security Center vous donne une liste des recommandations qui, lorsqu’elles sont appliquées, améliorent la sécurité de votre environnement. La liste est triée en fonction de l’impact sur le degré de sécurisation global, du plus élevé au plus bas. Le classement de la liste par impact vous permet de vous concentrer sur les recommandations ayant la priorité la plus élevée qui présentent le plus de valeur dans l’amélioration de votre sécurité.

Azure Policy joue également un rôle essentiel dans l’évaluation continue des vulnérabilités. Les types de stratégie disponibles dans Azure Policy vont de l’application de catégories et de valeurs de ressource, à la restriction des régions Azure dans lesquelles les ressources peuvent être créées, au blocage de la création de types de ressources spécifiques. Un ensemble de stratégies Azure peut être regroupé en initiatives. Les initiatives sont utilisées pour appliquer des stratégies Azure associées qui, lorsqu’elles sont appliquées en tant que groupe, constituent la base d’un objectif de sécurité ou de conformité particulier.

Azure Policy dispose d’une bibliothèque de définitions de stratégie intégrées dont le nombre augmente en permanence. Le portail Azure vous donne également la possibilité de créer vos propres définitions Azure Policy personnalisées. Une fois que vous avez trouvé une stratégie dans la bibliothèque existante ou que vous en avez créé une, vous pouvez attribuer la stratégie à des ressources Azure. Ces attributions peuvent être [étendues](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage) à différents niveaux dans la hiérarchie de gestion des ressources. L’attribution de stratégie est héritée, ce qui signifie que toutes les ressources enfants au sein d’une étendue reçoivent la même attribution de stratégie. Les ressources peuvent également être exclues de l’attribution de stratégie étendue, si nécessaire.

Toutes les stratégies Azure déployées contribuent au degré de sécurisation d’une organisation. Dans un environnement hautement dédié, des définitions Azure Policy personnalisées peuvent être créées et déployées pour fournir des informations d’audit adaptées à des charges de travail spécifiques.

## <a name="getting-started"></a>Prise en main

Pour commencer avec Azure Security Center et tirer pleinement d’Azure Monitor, d’Advisor et d’Azure Policy, Microsoft recommande les étapes initiales suivantes :

* Activation d’Azure Security Center
* Passer au niveau Standard
* Activer le provisionnement automatique de Microsoft Monitoring Agent pour les machines virtuelles Azure prises en charge
* Examiner, hiérarchiser et atténuer les suggestions et les alertes de sécurité sur le tableau de bord du centre de sécurité

## <a name="next-steps"></a>Étapes suivantes

Lisez la documentation relative à [Azure Policy et Azure Blueprints](azure-policy.md) pour plus d’informations sur l’implémentation de la gouvernance et du contrôle sur vos ressources Azure Australie pour garantir la conformité aux stratégies et aux réglementations.
