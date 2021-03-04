---
title: Prise en main de l’explorateur de métriques Azure
description: Découvrez comment créer votre premier graphique de métrique avec l’explorateur de métriques Azure.
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 68a9dd5cb24dda54e867580cf7c1bc869e692833
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101734415"
---
# <a name="getting-started-with-azure-metrics-explorer"></a>Prise en main d’Azure Metrics Explorer

## <a name="where-do-i-start"></a>Par où commencer ?
Azure Monitor Metrics Explorer est un composant du portail Microsoft Azure qui permet de tracer des graphiques, de corréler visuellement des tendances et d’investiguer sur les pics et les creux des valeurs de métriques. Utilisez l’explorateur de métriques pour examiner l’intégrité et l’utilisation de vos ressources. Procédez dans l’ordre suivant :

1. [Choisissez une ressource et une métrique](#create-your-first-metric-chart), un graphique de base s’affiche. Ensuite, [sélectionnez un intervalle de temps](#select-a-time-range) pertinent pour votre recherche.

1. Essayez d’[appliquer des filtres de dimension et un fractionnement](#apply-dimension-filters-and-splitting). Les filtres et le fractionnement vous permettent d’analyser les segments de la métrique contribuant à la valeur métrique globale et d’identifier les valeurs hors norme possibles.

1. Utilisez des [paramètres avancés](#advanced-chart-settings) pour personnaliser le graphique avant de l’épingler à des tableaux de bord. [Configurez des alertes](../alerts/alerts-metric-overview.md) pour recevoir des notifications lorsque la valeur de métrique est supérieure ou inférieure à un seuil.

## <a name="create-your-first-metric-chart"></a>Créer votre premier graphique de métrique

Pour créer un graphique de métrique, à partir de votre ressource, groupe de ressources, abonnement ou vue Azure Monitor, ouvrez l’onglet **Métriques** et procédez comme suit :

1. Cliquez sur le bouton « Sélectionner une étendue » pour ouvrir le sélecteur d’étendues de ressources. Ceci vous permettra de sélectionner la ou les ressources pour lesquelles vous souhaitez voir les métriques. La ressource doit être déjà remplie si vous avez ouvert Metrics Explorer à partir du menu de la ressource. Pour savoir comment voir les métriques de plusieurs ressources, [lisez cet article](./metrics-dynamic-scope.md).
    > ![Sélectionner une ressource](./media/metrics-getting-started/scope-picker.png)

2. Pour certaines ressources, vous devez choisir un espace de noms. L’espace de noms est simplement une méthode d’organisation des métriques pour que vous puissiez les retrouver facilement. Par exemple, les comptes de stockage ont des espaces de noms distincts pour le stockage des métriques de fichiers, tables, objets Blob et files d’attente. Plusieurs types de ressources n’ont qu’un seul espace de noms.

3. Sélectionnez une métrique dans la liste des métriques disponibles.

    > ![Sélectionner une métrique](./media/metrics-getting-started/metrics-dropdown.png)

4. Vous pouvez également, si vous le souhaitez, [modifier l’agrégation des métriques](../essentials/metrics-charts.md#aggregation). Par exemple, vous souhaiterez peut-être que votre graphique représente les valeurs minimale, maximale ou moyenne de la métrique.

> [!TIP]
> Utilisez le bouton **Ajouter une métrique** et répétez ces étapes si vous souhaitez afficher plusieurs métriques tracées dans le même graphique. Pour plusieurs graphiques dans une même vue, sélectionnez le bouton **Ajouter un graphique** en haut.

## <a name="select-a-time-range"></a>Sélectionner un intervalle de temps

> [!WARNING]
> [La plupart des métriques dans Azure sont stockées 93 jours](../essentials/data-platform-metrics.md#retention-of-metrics). Toutefois, vos interrogations peuvent porter sur 30 jours de données au maximum pour un même graphique. Cette limitation ne s’applique pas aux [métriques reposant sur un journal](../app/pre-aggregated-metrics-log-metrics.md#log-based-metrics).

Par défaut, le graphique affiche les dernières 24 heures de données de métriques. Utilisez le panneau du **sélecteur d’heure** pour modifier l’intervalle de temps, effectuer un zoom avant ou arrière sur votre graphique. 

![Modifier le panneau d’intervalle de temps](./media/metrics-getting-started/time.png)

> [!TIP]
> Utilisez l’outil **Balayage temporel** pour étudier une zone intéressante du graphique (pic ou baisse). Placez le pointeur de la souris au début de la zone, cliquez et maintenez le bouton gauche de la souris enfoncé, faites-le glisser de l'autre côté de la zone, puis relâchez le bouton. Cet intervalle de temps sera agrandi dans le graphique. 

## <a name="apply-dimension-filters-and-splitting"></a>Appliquer des filtres de dimension et un fractionnement

Le [filtrage](../essentials/metrics-charts.md#filters) et le [fractionnement](../essentials/metrics-charts.md#apply-splitting) sont des outils de diagnostic puissants pour les métriques comportant des dimensions. Ces fonctionnalités montrent comment les différents segments de métriques (« valeurs de dimension ») affectent la valeur globale de la métrique, et vous permettent d’identifier les valeurs hors norme possibles.

- Le **filtrage** vous permet de choisir les valeurs de dimension à inclure dans le graphique. Par exemple, vous souhaiterez peut-être afficher les requêtes réussies dans la représentation graphique de la métrique du *temps de réponse du serveur*. Vous devrez appliquer le filtre à la dimension de *succès de la requête*. 

- Le **fractionnement** contrôle si le graphique affiche des lignes distinctes pour chaque valeur d’une dimension ou agrège les valeurs dans une seule ligne. Par exemple, vous pouvez voir une ligne pour le temps de réponse moyen sur toutes les instances de serveur, ou voir des lignes distinctes pour chaque serveur. Vous devrez appliquer le fractionnement à la dimension d’*instance de serveur* pour voir des lignes distinctes.

Consultez les [exemples de graphiques](../essentials/metric-chart-samples.md) auxquels un filtrage et un fractionnement sont appliqués. L’article décrit les étapes utilisées pour configurer les graphiques.

## <a name="advanced-chart-settings"></a>Paramètres de graphique avancés

Vous pouvez personnaliser le style et le titre du graphique, et modifier les paramètres de graphique avancés. Lorsque vous avez terminé la personnalisation, épinglez-le à un tableau de bord pour enregistrer votre travail. Vous pouvez également configurer des alertes de métriques. Consultez la [documentation du produit](../essentials/metrics-charts.md) pour en savoir plus sur ces fonctionnalités et d’autres fonctionnalités avancées de l’explorateur de métriques Azure Monitor.

## <a name="next-steps"></a>Étapes suivantes

* [Découvrir les fonctionnalités avancées de Metrics Explorer](../essentials/metrics-charts.md)
* [Affichage de plusieurs ressources dans Metrics Explorer](./metrics-dynamic-scope.md)
* [Résolution des problèmes de Metrics Explorer](metrics-troubleshoot.md)
* [Consulter la liste des métriques disponibles pour les services Azure](./metrics-supported.md)
* [Consulter des exemples de graphiques configurés](../essentials/metric-chart-samples.md)