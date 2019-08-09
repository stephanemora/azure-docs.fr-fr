---
title: Conformité aux normes de sécurité d’Azure Policy et d’Azure Blueprints
description: Garantir la conformité et veillez à l’application des règles de sécurité d’Azure Policy et d’Azure Blueprints pour les agences gouvernementales australiennes concernant ASD ISM et Essential 8
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 0eb3e669f622a9c09c903530cbbab19206ce227f
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68571205"
---
# <a name="security-compliance-with-azure-policy-and-azure-blueprints"></a>Conformité aux normes de sécurité d’Azure Policy et d’Azure Blueprints

L’application des politiques de gouvernance dans l’environnement informatique est un véritable défi pour toutes les organisations, qu’il s’agisse d’un environnement local, natif dans le cloud ou hybride. Il est nécessaire de mettre en place un solide cadre de gouvernance technique pour garantir que vos environnements Microsoft Azure sont conformes aux exigences de conceptions, de réglementations et de sécurité.

Pour les agences gouvernementales australiennes, les contrôles clés à prendre en compte lors de l’évaluation des risques se trouvent dans le [Manuel de sécurité (ISM) du Centre de cybersécurité australien](https://acsc.gov.au/infosec/ism/index.htm) (ACSC). La majorité des contrôles décrits dans ISM exigent que la gouvernance technique soit gérée et appliquée efficacement. Il est important que vous disposiez des outils appropriés pour évaluer et appliquer la configuration dans vos environnements.

Microsoft Azure vous propose deux services complémentaires pour vous aider à relever ces défis : Azure Policy et Azure Blueprints.

## <a name="azure-policy"></a>Azure Policy

Azure Policy facilite l’application des éléments techniques de la gouvernance informatique d’une organisation. Azure Policy contient une bibliothèque de stratégies intégrées en constante évolution. Chaque stratégie applique des règles et des impacts sur les ressources Azure ciblées.

Une fois qu’une stratégie est attribuée aux ressources, la conformité globale de cette stratégie peut être évaluée et corrigée si nécessaire.

Cette bibliothèque de stratégies Azure intégrées permet à une organisation d’appliquer rapidement les types de contrôle disponibles dans ACSC ISM. Voici quelques exemples de contrôles :

* Surveillance de machines virtuelles pour des mises à jour de système manquantes
* Audit de comptes avec des autorisations élevées pour l’authentification multifacteur
* Détection de bases de données SQL non chiffrées
* Surveillance de l’utilisation du contrôle d’accès en fonction du rôle (RBAC) personnalisé Azure
* Restriction des régions Azure dans lesquelles les ressources peuvent être créées

Si les contrôles de gouvernance ou réglementaires ne sont pas pris en charge par une définition d’Azure Policy intégrée, une définition personnalisée peut être créée et attribuée. Toutes les définitions d’Azure Policy sont définies au format JSON et suivent une [structure de définition](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure) standard. Les définitions d’Azure Policy existantes peuvent également être dupliquées et utilisées comme base d’une définition de stratégie personnalisée.

L’attribution de stratégies Azure individuelles à des ressources, en particulier dans des environnements complexes ou soumis à des exigences réglementaires strictes, peut engendrer des surcharges conséquentes pour vos administrateurs. Pour vous aider à relever ces défis, vous pouvez regrouper un ensemble de stratégies Azure pour créer une initiative Azure Policy. Les initiatives de stratégie sont utilisées pour combiner des stratégies Azure associées qui, lorsqu’elles sont appliquées en tant que groupe, constituent la base d’un objectif de sécurité ou de conformité particulier. Microsoft ajoute des définitions d’initiative Azure Policy Initiative intégrées, y compris des définitions conçues pour répondre à des exigences réglementaires spécifiques :

![Initiatives en matière de politique de conformité réglementaire](media/regulatory-initiatives.png)

Toutes les stratégies et les initiatives Azure sont attribuée à une étendue d’attribution. Cette étendue est définie au niveau de l’abonnement Azure, du groupe d’administration Azure ou des groupes de ressources Azure. Une fois que les stratégies ou les initiatives de stratégie Azure Policy requises ont été attribuées, une organisation peut appliquer la configuration requise pour toutes les ressources Azure nouvellement créées.

L’attribution d’une nouvelle stratégie ou initiative Azure Policy n’a aucun impact pour les ressources Azure existantes. Azure Policy peut cependant permettre à une organisation d’afficher la conformité des ressources Azure existantes. Toutes les ressources ayant été reconnues comme non conformes peuvent être corrigées à la discrétion de l’organisation

### <a name="azure-policy-and-initiatives-in-action"></a>Stratégie et initiatives Azure Policy en pratique

Les définitions des initiatives et des stratégies Azure Policy intégrées disponibles se trouvent dans le nœud Définition dans la section Stratégie du portail Azure :

![Définitions Azure Policy intégrées](media/policy-definitions.png)

À l’aide de la bibliothèque de définitions intégrées, vous pouvez rapidement rechercher des stratégies qui répondent à une exigence organisationnelle, revoir la définition de la stratégie et attribuer la stratégie aux ressources appropriées. Par exemple, ISM requiert l’authentification multifacteur (MFA) pour tous les utilisateurs disposant de privilèges et pour tous ceux ayant accès à des référentiels de données importants. Dans Azure Policy, vous pouvez rechercher « MFA » parmi les définitions Azure Policy :

![Stratégies d’authentification multifacteur Azure](media/mfa-policies.png)

Après avoir trouvé une stratégie appropriée, vous pouvez affecter la stratégie à l’étendue souhaitée. Si aucune stratégie intégrée ne répond à vos exigences, vous pouvez dupliquer la stratégie existante et apporter les modifications souhaitées :

![Dupliquer une stratégie Azure Policy existante](media/duplicate-policy.png)

Microsoft fournit également un ensemble d’exemples Azure Policy sur [GitHut](https://github.com/Azure/azure-policy) pour un « démarrage rapide » vous permettant de créer des stratégies Azure personnalisées. Ces exemples de stratégie peuvent être copiés directement dans l’éditeur d’Azure Policy dans le portail Microsoft Azure.

Lorsque vous créez des initiatives Azure Policy, vous pouvez trier la liste des définitions de stratégie disponibles, qu’elles soient intégrées ou personnalisées, en ajoutant les définitions requises.

Par exemple, vous pouvez effectuer une recherche dans la liste des définitions Azure Policy disponibles pour toutes les stratégies relatives aux machines virtuelles de Windows. Vous pouvez ensuite appliquer ces définitions à une initiative conçue pour appliquer les pratiques de renforcement des machines virtuelles recommandées :

![Liste des stratégies Azure](media/initiative-definitions.png)

Lors de l’attribution d’une stratégie ou de l’initiative d’une stratégie Azure Policy à une étendue d’attribution, vous pouvez exclure les ressources Azure des impacts des stratégies en excluant les groupes d’administration Azure ou les groupe de ressources Azure.

### <a name="real-time-enforcement-and-compliance-assessment"></a>Évaluation de la mise en œuvre et de la conformité en temps réel

Les analyses de conformité d’Azure Policy des ressources Azure comprises dans l’étendue sont effectuées lorsque les conditions suivantes sont réunies :

* Lorsqu’une stratégie ou une initiative de stratégie Azure Policy est attribuée
* Lorsque l’étendue d’une stratégie ou d’une initiative de stratégie Azure Policy existante est modifiée
* Sur demande via l’API jusqu’à un maximum de 10 analyses par heure
* Une fois toutes les 24 heures, le comportement par défaut

Une analyse de conformité de la stratégie pour une ressource Azure unique est effectuée 15 minutes après qu’une modification a été apportée à la ressource.

Vous pouvez consulter la vue d’ensemble de la conformité des ressources Azure Policy dans le Portail Azure à l’aide du tableau de bord de conformité des stratégies :

![Score de conformité d’Azure Policy](media/simple-compliance.png)

La pourcentage global de conformité des ressources est un agrégat de la conformité de toutes les ressources déployées dans l’étendue par rapport à l’ensemble des stratégies Azure attribuées. Cela vous permet de détecter les ressources dans un environnement qui n’est pas conforme et de concevoir le plan qui permettra de mieux corriger ces ressources.

Le tableau de bord de conformité des stratégies comprend également l’historique des modifications pour chaque ressource. Si une ressource est reconnue comme n’étant plus conforme à la stratégie attribuée et que la correction automatique n’est pas activée, vous pouvez voir qui a effectué la modification, ce qui a été modifié et la date à laquelle les modifications ont été apportées à cette ressource.

## <a name="azure-blueprints"></a>Azure Blueprints

Azure Blueprints renforce les fonctionnalités d’Azure Policy en les associant aux éléments suivants :

* Azure RBAC
* Groupes de ressources Azure
* [Modèles Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)

Les blueprints permettent de créer des conceptions d’environnement qui déploient des ressources Azure à partir de modèles de gestionnaires de ressources, de configurer RBAC ainsi que d’appliquer et d’auditer la configuration en attribuant Azure Policy. Les blueprints forment un modèle d’environnement modifiable et redéployable. Une fois le blueprint créé, il peut être attribué à un abonnement Azure. Une fois attribuées, l’ensemble des ressources Azure définies dans le blueprint sont créées et les stratégies Azure sont appliquées. Le déploiement et la configuration des ressources définies dans Azure Blueprint peuvent être surveillés à partir de la console Azure Blueprint dans le Portail Azure.

Les Azure Blueprints ayant été modifiés doivent être publiés de nouveau dans le portail Microsoft Azure. Chaque fois qu’un blueprint est publié, son numéro de version est incrémenté. Le numéro de version vous permet de déterminer quelle version du blueprint a été déployée sur les abonnements Azure d’une organisation. Si vous le souhaitez, la version du blueprint actuellement attribué peut être mise à jour vers la version la plus récente.

Les ressources déployées à l’aide d’Azure Blueprint peuvent être configurées avec [des verrous de ressources Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) lors du déploiement. Les verrous de ressources empêchent la modification ou la suppression accidentelle des ressources.

Microsoft prépare actuellement des modèles Azure Blueprint pour un éventail de secteurs d’activités et d’exigences réglementaires. La bibliothèque actuelle de définitions Azure Blueprint disponibles peut être affichée dans la portail Azure ou sur la page [Blueprint de sécurité et de conformité Azure](https://servicetrust.microsoft.com/ViewPage/BlueprintOverview/) dans le portail Service Trust.

### <a name="azure-blueprint-artifacts"></a>Artefacts de blueprint Azure

Pour créer un blueprint Azure, vous pouvez commencer par un modèle blueprint vierge ou utiliser l’un des exemples de modèles existants comme point de départ. Vous pouvez ajouter des artefacts au blueprint qui sera configuré lors du déploiement :

![Artefacts de blueprint Azure](media/blueprint-artifacts.png)

Ces artefacts peuvent inclure le groupe de ressources Azure et les ressources, ainsi que les stratégies et les initiatives de stratégie Azure Policy dans le but de faire appliquer la configuration requise afin que votre environnement soit conforme à vos exigences réglementaires, telles que les contrôles ISM pour le renforcement du système.

Chacun de ces artefacts peut également être configuré avec des paramètres. Ces valeurs sont fournies lorsque le blueprint est attribué à un abonnement Azure et déployé. Les paramètres permettent de créer et d’utiliser un seul blueprint pour déployer des ressources dans différents environnements sans avoir à modifier le blueprint sous-jacent.

Microsoft élabore des cmdlets Azure PowerShell et CLI pour créer et gérer des modèles Azure Blueprint et a pour objectif qu’un Blueprint puisse être maintenu et déployé par une organisation via un pipeline CI/CD.

## <a name="next-steps"></a>Étapes suivantes

Cet article explique comment la gouvernance et la sécurité peuvent être appliquées par Azure Policy et Azure Blueprints. Maintenant que vous avez été exposé à un niveau élevé, découvrez plus en détail comment utiliser chaque service :

* [Vue d’ensemble Azure Policy](https://docs.microsoft.com/azure/governance/policy/overview)
* [Vue d’ensemble Azure Blueprints](https://azure.microsoft.com/services/blueprints/)
* [Exemples Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/index)
* [Référentiel d’exemple Azure Policy](https://github.com/Azure/azure-policy)
* [Structure de définition Azure Policy](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure)
* [Impacts Azure Policy](https://docs.microsoft.com/azure/governance/policy/concepts/effects)
