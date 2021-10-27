---
title: Bonnes pratiques Azure Monitor
description: Aide et recommandations relatives au déploiement d’Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/18/2021
ms.openlocfilehash: 2caff70e56f80d4f46859cec58275263375ee4de
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130181690"
---
# <a name="azure-monitor-best-practices"></a>Bonnes pratiques Azure Monitor
Ce scénario fournit une aide pour configurer les fonctionnalités d’Azure Monitor afin de surveiller les performances et la disponibilité de vos applications et ressources cloud et hybrides. 

Azure Monitor est disponible dès que vous créez un abonnement Azure. Le journal d’activité commence immédiatement à collecter les événements relatifs à l’activité de l’abonnement, et des métriques de plateforme sont collectées pour toutes les ressources Azure que vous avez créées. Des fonctionnalités telles que Metrics Explorer sont disponibles pour analyser les données. D’autres fonctionnalités requièrent une configuration. Ce scénario identifie les étapes de configuration requises pour tirer parti de toutes les fonctionnalités d’Azure Monitor. Il formule également des recommandations sur les fonctionnalités que vous devez exploiter et sur la manière de déterminer les options de configuration en fonction de vos besoins spécifiques.

L’activation d’Azure Monitor pour surveiller toutes vos ressources Azure consiste en une combinaison de configuration de composants Azure Monitor et de configuration des ressources Azure pour générer des données d’analyse collectées par Azure Monitor. L’objectif d’une implémentation complète consiste à collecter toutes les données utiles de toutes vos ressources et applications cloud et à activer l’ensemble des fonctionnalités d’Azure Monitor en fonction de ces données.


> [!IMPORTANT]
> Si vous ne connaissez pas Azure Monitor ou si vous souhaitez simplement surveiller une seule ressource Azure, nous vous conseillons de commencer par le tutoriel [Superviser des ressources Azure avec Azure Monitor](/essentials/monitor-azure-resource.md). Ce tutoriel fournit des concepts généraux pour Azure Monitor et une aide pour la surveillance d’une seule ressource Azure. Ce scénario fournit des recommandations pour la préparation de votre environnement afin de tirer parti de toutes les fonctionnalités d’Azure Monitor pour surveiller l’ensemble de vos applications et de vos ressources à grande échelle.

## <a name="scope-of-the-scenario"></a>Portée du scénario
L’objectif de ce scénario est de vous guider à travers les étapes de base d’une implémentation complète d’Azure Monitor afin de vous assurer que vous tirez pleinement parti de ses fonctionnalités et que vous optimisez l’observabilité de vos applications et ressources cloud et hybrides. Il se concentre sur les exigences de configuration et les options de déploiement, par opposition aux détails de la configuration proprement dite. Vous trouverez des liens renvoyant à d’autres contenus qui fournissent les détails nécessaires à la réalisation de la configuration requise.

## <a name="scenario-articles"></a>Articles du scénario
Cet article présente le scénario. Si vous souhaitez accéder directement à une zone spécifique, consultez un des autres articles qui font partie de ce scénario décrit dans le tableau suivant.

| Article | Description |
|:---|:---|
| [Planification](best-practices-plan.md)  | Planification que vous devez prendre en compte avant de commencer votre implémentation. Comprend des décisions de conception et les informations sur votre organisation et vos exigences que vous devez recueillir. |
| [Configurer la collecte de données](best-practices-data-collection.md) | Tâches nécessaires pour collecter les données de surveillance de vos applications et ressources Azure et hybrides. |
| [Analyse et visualisations](best-practices-analysis.md) | Fonctionnalités standard et visualisations supplémentaires que vous pouvez créer pour analyser les données de surveillance collectées. |
| [Alertes et réponses automatisées](best-practices-alerts.md) | Configurez les notifications et les processus qui sont automatiquement déclenchés lors de la création d’une alerte. |




## <a name="next-steps"></a>Étapes suivantes

- [Planification de la stratégie et de la configuration de la surveillance](best-practices-plan.md)