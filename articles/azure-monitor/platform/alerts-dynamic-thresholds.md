---
title: Création d’alertes avec des seuils dynamiques dans Azure Monitor
description: Créer des alertes avec des seuils dynamiques basés sur le machine learning
author: yanivlavi
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: yalavi
ms.reviewer: mbullwin
ms.openlocfilehash: 4024ecddde4b0d020e2c657214a4a258ea0b2ea5
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54449008"
---
# <a name="metric-alerts-with-dynamic-thresholds-in-azure-monitor-public-preview"></a>Alertes de métrique avec des seuils dynamiques dans Azure Monitor (préversion publique)

L’alerte de métrique avec détection des seuils dynamiques s’appuie sur le machine learning avancé pour apprendre le comportement historique des métriques et identifier des modèles et des anomalies indiquant d’éventuels problèmes de service. Elle propose de prendre en charge à la fois une interface utilisateur simple et des opérations à grande échelle en permettant aux utilisateurs de configurer des règles d’alerte par le biais de l’API Azure Resource Manager, de manière totalement automatisée.

Une fois qu’une règle d’alerte est créée, elle se déclenche uniquement quand la métrique supervisée ne se comporte pas comme prévu, en fonction de ses seuils personnalisés.

Nous aimerions beaucoup avoir votre avis, envoyez vos commentaires à azurealertsfeedback@microsoft.com.

## <a name="why-and-when-is-using-dynamic-condition-type-recommended"></a>Quand et pourquoi l’utilisation d’un type de condition dynamique est-elle recommandée ?

1. **Alertes scalables** : Les règles d’alerte avec des seuils dynamiques peuvent créer des seuils adaptés à des centaines de séries de métriques à la fois. Tout en fournissant quand même la même facilité de définition d’une règle d’alerte sur une seule métrique. L’utilisation de l’interface utilisateur ou de l’API Azure Resource Manager permet d’avoir moins de règles d’alerte à gérer. L’approche scalable s’avère particulièrement utile lorsque vous traitez des dimensions de métrique ou plusieurs ressources, comme toutes les ressources d’abonnement. Ce qui se traduit par un gain de temps important dans la gestion et la création des règles d’alerte. [Découvrez comment configurer des alertes de métrique avec des seuils dynamiques à l’aide de modèles](alerts-metric-create-templates.md).

1. **Reconnaissance intelligente des modèles de métriques** : avec notre technologie de machine learning unique, nous sommes en mesure de détecter automatiquement des modèles de métriques pour nous adapter à leur évolution au fil du temps, ce qui peut souvent inclure leur saisonnalité (toutes les heures/tous les jours/toutes les semaines). L’adaptation au comportement des métriques au fil du temps et les alertes émises selon les écarts par rapport à un modèle permettent de ne pas avoir à connaître le « bon » seuil de chaque métrique. L’algorithme ML utilisé dans les seuils dynamiques est conçu pour empêcher les seuils bruyants (faible précision) ou larges (faible rappel) qui n’ont pas de modèle attendu.

1. **Configuration intuitive** : Les seuils dynamiques permettent de configurer des alertes de métrique avec des concepts généraux, ce qui limite le besoin de connaissances spécifiques approfondies sur la métrique.

## <a name="how-to-configure-alerts-rules-with-dynamic-thresholds"></a>Comment configurer des règles d’alerte avec des seuils dynamiques ?

Vous pouvez configurer des alertes avec des seuils dynamiques par le biais des alertes de métrique dans Azure Monitor. [Découvrez comment configurer des alertes de métrique](alerts-metric.md).

## <a name="how-are-the-thresholds-calculated"></a>Comment les seuils sont-ils calculés ?

Un seuil dynamique apprend sans discontinuer les données de la série de métriques pour essayer de les modéliser à l’aide d’un ensemble d’algorithmes et de méthodes. Il détecte des modèles dans les données comme une saisonnalité (toutes les heures/tous les jours/toutes les semaines) et est capable de gérer des métriques bruyantes (comme le processeur ou la mémoire de l’ordinateur), ainsi que des métriques à faible dispersion (comme la disponibilité et le taux d’erreur).

Les seuils sont sélectionnés de manière à ce qu’un écart par rapport à ces seuils indique une anomalie dans le comportement de la métrique.

## <a name="what-does-sensitivity-setting-in-dynamic-thresholds-mean"></a>Que signifie le paramètre « Sensibilité » dans les seuils dynamiques ?

La sensibilité d’un seuil d’alerte est un concept général qui contrôle l’ampleur de l’écart par rapport à au comportement de la métrique nécessaire pour déclencher une alerte.
Cette option ne nécessite pas de connaissances spécifiques sur la métrique comme le seuil statique. Voici les options disponibles :

- Élevée : Les seuils sont stricts et proches du modèle de la série de métriques. La règle d’alerte est déclenchée dès le plus petit écart, ce qui génère un nombre plus important d’alertes.
- Moyenne : Les seuils sont moins stricts et plus équilibrés, les alertes moins nombreuses qu’avec une sensibilité élevée (par défaut).
- Faible : Les seuils sont lâches et plus distants du modèle de la série de métriques. La règle d’alerte se déclenche uniquement quand l’écart est grand, ce qui génère moins d’alertes.

## <a name="what-are-the-operator-setting-options-in-dynamic-thresholds"></a>Quelles sont les options du paramètre « Opérateur » dans les seuils dynamiques ?

La règle d’alerte des seuils dynamiques permet de créer des seuils adaptés au comportement de la métrique dans ses limites supérieures et inférieures avec la même règle d’alerte.
Vous pouvez choisir l’alerte à déclencher dans l’une des trois conditions suivantes :

- Supérieur au seuil supérieur ou inférieur au seuil inférieur (par défaut)
- Supérieur au seuil supérieur
- Inférieur au seuil inférieur

## <a name="what-do-the-advanced-settings-in-dynamic-thresholds-mean"></a>Que signifient les paramètres avancés dans les seuils dynamiques ?

**Périodes d’échec** : Les seuils dynamiques vous permettent également de configurer le « nombre de violations avant de déclencher l’alerte », qui correspond à un nombre minimal d’écarts nécessaire pendant une certaine fenêtre de temps pour que le système déclenche une alerte (la fenêtre de temps par défaut est de quatre écarts en 20 minutes). L’utilisateur peut configurer des périodes d’échec et choisir sur quoi être alerté en modifiant les périodes d’échec et la fenêtre de temps. Cette possibilité permet de réduire le nombre d’alertes générées par des pics passagers. Par exemple : 

Pour déclencher une alerte quand le problème perdure pendant 20 minutes, 4 fois de suite par groupe de périodes de 5 minutes, utilisez les paramètres suivants :

![Paramètres des périodes d’échec pour un problème qui perdure pendant 20 minutes, 4 fois de suite par groupe de périodes de 5 minutes](media/alerts-dynamic-thresholds/0008.png)

Pour déclencher une alerte en cas de violation d’un seuil dynamique pendant 20 minutes sur les 30 dernières minutes avec un groupe de périodes de 5 minutes, utilisez les paramètres suivants :

![Paramètres des périodes d’échec pour un problème qui perdure pendant 20 minutes sur les 30 dernières minutes par groupe de périodes de 5 minutes](media/alerts-dynamic-thresholds/0009.png)

**Ignorer les données précédentes** : Les utilisateurs peuvent éventuellement définir aussi une date de début à partir de laquelle le système doit commencer à calculer les seuils. Un cas d’usage typique peut se produire quand une ressource était exécutée en mode de test et qu’elle est à présent promue pour répondre à une charge de travail de production. Ainsi, le comportement de toute métrique pendant la phase de test doit être ignoré.

## <a name="will-slow-behavior-change-in-the-metric-trigger-an-alert"></a>Une modification de comportement lente dans la métrique déclenche-t-elle une alerte ?

Probablement pas. Les seuils dynamiques excellent à détecter des écarts significatifs plutôt que des problèmes à évolution lente.

## <a name="how-much-data-is-used-to-preview-and-then-calculate-thresholds"></a>Quelle quantité de données est utilisée pour prévoir, puis calculer les seuils ?

Les seuils figurant dans le graphique, avant qu’une règle d’alerte ne soit créée sur la métrique, sont calculés sur la base des données historiques des 10 derniers jours. Une fois qu’une règle d’alerte est créée, les seuils dynamiques acquièrent de nouvelles données historiques supplémentaires, qu’ils apprennent en continu afin d’améliorer leur précision.
