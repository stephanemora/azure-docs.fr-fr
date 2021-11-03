---
title: 'Démarrage rapide : Portail web de Metrics Advisor'
titleSuffix: Azure Cognitive Services
description: Découvrez comment commencer à utiliser le portail web de Metrics Advisor.
services: cognitive-services
author: mrbullwinkle
ms.author: mbullwin
manager: nitinme
ms.date: 09/30/2020
ms.topic: quickstart
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.custom: mode-portal
ms.openlocfilehash: ca4edde32a8a1eb490eb8c62d23c22061ea5c706
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131026616"
---
# <a name="quickstart-monitor-your-first-metric-by-using-the-web-portal"></a>Démarrage rapide : Surveiller votre première métrique à l’aide du portail web

Lorsque vous provisionnez une instance Azure Metrics Advisor, vous pouvez utiliser les API et l’espace de travail web pour travailler avec ce service. L’espace de travail web peut être utilisé comme un moyen simple de rapidement prendre en main le service. Il fournit également un moyen visuel de configurer les paramètres, de personnaliser votre modèle et d’effectuer une analyse de la cause racine. 

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. [Créez-en un gratuitement](https://azure.microsoft.com/free/cognitive-services).
* Une fois que vous disposez de votre abonnement Azure, <a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title="Créer une ressource Metrics Advisor"  target="_blank">créez une ressource Metrics Advisor</a> dans le portail Azure pour déployer votre instance Metrics Advisor.  

    
> [!TIP]
> * 10 à 30 minutes peuvent être nécessaires pour le déploiement de votre ressource Metrics Advisor. Sélectionnez **Accéder à la ressource** une fois le déploiement réussi.
> * Si vous souhaitez utiliser l’API REST pour interagir avec le service, vous aurez besoin de la clé et du point de terminaison de la ressource que vous créez. Vous pouvez les trouver sous l’onglet **Clés et points de terminaison**, dans la ressource créée.


Ce document utilise une base de données SQL comme exemple pour la création de votre premier moniteur.

## <a name="sign-in-to-your-workspace"></a>Vous connecter à votre espace de travail

Après avoir créé votre ressource, connectez-vous au [portail Metrics Advisor](https://go.microsoft.com/fwlink/?linkid=2143774) avec votre compte Active Directory. Dans la page d’accueil, sélectionnez votre **annuaire**, votre **abonnement** et l’**espace de travail** que vous venez de créer, puis sélectionnez **Prise en main**. Pour utiliser des données de séries chronologiques, sélectionnez **Ajouter un flux de données** dans le menu de gauche.

 
Vous pouvez créer une ressource Metrics Advisor à chaque région disponible. Vous pouvez changer d’espace de travail dans le portail Metrics Advisor à tout moment.


## <a name="time-series-data"></a>Données de séries chronologiques

Metrics Advisor fournit des connecteurs pour différentes sources de données comme Azure SQL Database, Azure Data Explorer ou le stockage Table Azure. Les étapes de connexion des données sont similaires pour les différents connecteurs, même si certains paramètres de configuration peuvent varier. Pour plus d’informations, consultez [Connecter différentes sources de données](../data-feeds-from-different-sources.md).

Ce guide de démarrage rapide utilise une base de données SQL comme exemple. Vous pouvez également ingérer vos propres données en suivant la même procédure.


### <a name="data-schema-requirements-and-configuration"></a>Configuration requise et configuration des schémas de données

[!INCLUDE [data schema requirements](../includes/data-schema-requirements.md)]

### <a name="configure-connection-settings-and-query"></a>Configurer les paramètres de connexion et les requêtes

[Ajoutez le flux de données](../how-tos/onboard-your-data.md) en vous connectant à votre source de données de série chronologique. Commencez par sélectionner les paramètres suivants :

* **Type de source** : Type de source de données dans laquelle vos données de série chronologique sont stockées.
* **Granularité** : Intervalle entre des points de données consécutifs dans vos données de série chronologique (par exemple, Annuel, Mensuel ou Quotidien). L’intervalle le plus faible pris en charge est de 60 secondes.
* **Ingérer des données depuis (UTC)**  : Heure de début du premier horodatage à ingérer. 


:::image type="content" source="../media/connection-settings.png" alt-text="Capture d’écran montrant les paramètres de connexion." lightbox="../media/connection-settings.png":::


### <a name="load-data"></a>Charger les données

Une fois que vous avez entré les chaînes de connexion et de requête, sélectionnez **Charger des données**. Metrics Advisor vérifie la connexion et l’autorisation de chargement des données, les paramètres nécessaires utilisés dans la requête et le nom de colonne de la source de données. 

En cas d’erreur à cette étape :
1. Vérifiez si la chaîne de connexion est valide. 
1. Vérifiez que les autorisations sont suffisantes et que l’accès est accordé à l’adresse IP du worker.
1. Vérifiez si les paramètres requis (`@IntervalStart` et `@IntervalEnd`) sont utilisés dans votre requête. 

### <a name="schema-configuration"></a>Configuration du schéma

Une fois les données chargées par l'exécution de la requête, sélectionnez les champs appropriés.


|Sélection  |Description  |Notes  |
|---------|---------|---------|
|**Timestamp**     | Horodatage d’un point de données. Si le timestamp est omis, Metrics Advisor utilise à la place l’horodatage de l’ingestion du point de données. Pour chaque flux de données, vous pouvez spécifier au plus une colonne comme horodateur.        | Optionnel. Doit être spécifié avec au plus une colonne.       |
|**Mesure**     |  Valeurs numériques dans le flux de données. Pour chaque flux de données, vous pouvez spécifier plusieurs unités, mais au moins une colonne doit être sélectionnée en tant qu’unité.        | Doit être spécifiée avec au moins une colonne.        |
|**Dimension**     | Valeurs catégorielles. Une combinaison de valeurs différentes identifie une série chronologique unidimensionnelle particulière. Les exemples incluent le pays, la langue et le locataire. Vous pouvez sélectionner un nombre nul ou arbitraire de colonnes comme dimensions. Si vous sélectionnez une colonne qui n’est pas une chaîne comme dimension, soyez vigilant avec l’explosion de dimension. | Optionnel.        |
|**Ignorer**     | Suppression de la colonne sélectionnée.        | Optionnel. Pour que les sources de données prennent en charge l’utilisation d’une requête pour obtenir des données, il n’existe aucune option « Ignorer ».       |


:::image type="content" source="../media/schema-configuration.png" alt-text="Capture d’écran montrant la configuration du schéma." lightbox="../media/schema-configuration.png":::

Après avoir configuré le schéma, sélectionnez **Vérifier le schéma**. Metrics Advisor effectue les vérifications suivantes :
- Indique si l’horodateur des données interrogées se trouve dans un seul intervalle. 
- Indique s’il existe des valeurs en double retournées pour la même combinaison de dimensions dans un intervalle de métrique.  

### <a name="automatic-roll-up-settings"></a>Paramètres de regroupement automatique

> [!IMPORTANT]
> Si vous souhaitez activer l’analyse de la cause racine et d’autres fonctionnalités de diagnostic, les paramètres de cumul automatique doivent être configurés. Une fois l’analyse activée, vous ne pouvez pas modifier les paramètres de regroupement automatique.

Metrics Advisor peut effectuer automatiquement l’agrégation sur chaque dimension lors de l’ingestion. Ensuite, le service crée une hiérarchie que vous pouvez utiliser dans l’analyse de la cause racine et d’autres fonctionnalités de diagnostic. Pour plus d’informations, consultez [Paramètres de regroupement automatique](../how-tos/onboard-your-data.md#automatic-roll-up-settings).

Donnez un nom personnalisé au flux de données, qui s’affichera dans votre espace de travail. Sélectionnez **Envoyer**. 

## <a name="tune-detection-configuration"></a>Ajuster la configuration de détection

Une fois le flux de données ajouté, Metrics Advisor tente d’ingérer les données de métrique à partir de la date de début spécifiée. Il faut un certain temps pour ingérer complètement les données et vous pouvez afficher l’état d’ingestion en sélectionnant **Progression de l’ingestion** en haut de la page de flux de données. Si les données sont ingérées, Metrics Advisor applique la détection et continue à surveiller la source pour détecter de nouvelles données.

Lorsque la détection est appliquée, sélectionnez l’une des métriques listées dans le flux de données pour rechercher la **page de détails des métriques**. Ici, vous pouvez : 
- Afficher les visualisations de toutes les tranches de série chronologique sous cette métrique.
- Mettre à jour la configuration de la détection afin de répondre aux résultats attendus.
- Configurer une notification pour les anomalies détectées.

:::image type="content" source="../media/metric-details.png" alt-text="Capture d’écran montrant les détails des métriques." lightbox="../media/metric-details.png":::

## <a name="view-diagnostic-insights"></a>Afficher les insights de diagnostic

Après avoir ajusté la configuration de la détection, vous devriez constater que les anomalies détectées reflètent les anomalies réelles de vos données. Metrics Advisor effectue une analyse sur les métriques multidimensionnelles pour localiser la cause racine d’une dimension spécifique. Le service effectue également une analyse inter-métriques à l’aide de la fonctionnalité de graphique de métriques. 

Pour afficher les insights de diagnostic, sélectionnez les points rouges sur les visualisations de séries chronologiques. Ces points rouges représentent les anomalies détectées. Une fenêtre s’affiche avec un lien vers la page d’analyse des incidents. 

:::image type="content" source="../media/incident-link.png" alt-text="Capture d’écran montrant un lien d’incident." lightbox="../media/incident-link.png":::

Sur la page d’analyse d’incident, vous pouvez voir un groupe d’anomalies et de diagnostics associés. Les sections suivantes couvrent les principales étapes de diagnostic d’un incident.

### <a name="check-the-summary-of-the-current-incident"></a>Vérifier le résumé de l'incident en cours

Vous pouvez trouver le résumé en haut de la page d'analyse des incidents. Ce résumé comprend des informations de base, des actions et des traces, ainsi qu’une cause racine analysée. Les informations de base incluent la « série Top impactée » avec un diagramme « impact Start & End Time », « gravité » et « nombre total d’anomalies incluses ».

La cause racine analysée est un résultat analysé automatiquement. Metrics Advisor analyse toutes les anomalies capturées sur les séries chronologiques au sein d’une métrique avec des valeurs de dimension différentes pour un même horodatage. Le service effectue ensuite la corrélation, le clustering pour regrouper les anomalies associées et générer un avis de cause racine.

:::image type="content" source="../media/diagnostics/incident-summary.png" alt-text="Capture d’écran montrant un résumé des diagnostics d’incident." lightbox="../media/diagnostics/incident-summary.png":::

Sur cette base, vous pouvez déjà obtenir une vue directe de l'état anormal actuel, de l'impact de l'incident et de la cause racine la plus potentielle. Vous pouvez ensuite prendre des mesures immédiates pour résoudre l’incident. 

### <a name="view-cross-dimension-diagnostic-insights"></a>Visualiser les aperçus du diagnostic transdimensionnel

Vous pouvez également obtenir des informations plus détaillées sur l’état anormal d’autres dimensions au sein de la même mesure de façon holistique, à l’aide de la fonctionnalité d’arborescence de diagnostic.

Pour les métriques avec plusieurs dimensions, le conseiller de métriques classe la série chronologique dans une hiérarchie (nommée « arborescence de diagnostic »). Par exemple, une mesure « chiffre d’affaires » est analysée par deux dimensions : « région » et « catégorie ». Vous devez disposer d’une valeur de dimension agrégée, telle que `SUM`. Ensuite, la série chronologique de `region = SUM` et `category = SUM` est catégorisée comme nœud racine dans l’arborescence. Lorsqu’une anomalie est capturée à la dimension `SUM`, vous pouvez l’analyser pour localiser la valeur de dimension spécifique qui a contribué le plus à l’anomalie du nœud parent. Sélectionnez chaque nœud à développer et consultez les informations détaillées.

:::image type="content" source="../media/diagnostics/cross-dimension-diagnostic.png" alt-text="Capture d’écran montrant la vue inter-dimensions des diagnostics d’incident." lightbox="../media/diagnostics/cross-dimension-diagnostic.png":::

### <a name="view-cross-metrics-diagnostic-insights"></a>Voir des insights de diagnostic inter-métriques

Parfois, il est difficile d’analyser un problème en vérifiant l’état anormal d’une mesure unique, et vous devez mettre en corrélation plusieurs métriques ensemble. Pour ce faire, configurez un « graphique de métriques » qui indique les relations entre les métriques. 

En utilisant le résultat de diagnostic inter-dimensions décrit dans la section précédente, vous pouvez déterminer que la cause racine est limitée à une valeur de dimension spécifique. Utilisez ensuite le « graphique de métriques » et filtrez en fonction de la dimension de la cause racine analysée pour vérifier l’état d’anomalie sur d’autres mesures.

:::image type="content" source="../media/diagnostics/cross-metrics-analysis.png" alt-text="Capture d’écran montrant l’analyse inter-métriques des diagnostics d’incident." lightbox="../media/diagnostics/cross-metrics-analysis.png":::

Vous pouvez également croiser d’autres informations de diagnostic en utilisant des fonctionnalités supplémentaires. Ces fonctionnalités vous permettent d’approfondir les dimensions des anomalies, d’afficher des anomalies similaires et de comparer les métriques. Pour plus d’informations, consultez [Diagnostic d’un incident](../how-tos/diagnose-an-incident.md). 

## <a name="get-notified-when-new-anomalies-are-found"></a>Recevoir une notification quand de nouvelles anomalies sont détectées

Si vous souhaitez être alerté quand une anomalie est détectée dans vos données, vous pouvez créer un abonnement pour une ou plusieurs de vos métriques. Metrics Advisor utilise des hooks pour envoyer des alertes. Trois types de hooks sont pris en charge : les hooks d’e-mail, les webhooks et Azure DevOps. Nous allons utiliser un webhook à titre d’exemple. 

### <a name="create-a-web-hook"></a>Créer un webhook

Dans Metrics Advisor, vous pouvez utiliser un webhook pour faire face à une anomalie par programmation. Le service appelle une API fournie par l’utilisateur lorsqu’une alerte est déclenchée. Pour plus d’informations, consultez [Créer un hook](../how-tos/alerts.md#create-a-hook). 

### <a name="configure-alert-settings"></a>Configuration des paramètres d'alerte

Après la création d’un hook, un paramètre d’alerte détermine quelles notifications envoyer et de quelle manière. Vous pouvez définir plusieurs paramètres d’alerte pour chaque métrique. Deux paramètres importants sont **Alerte pour** qui spécifie les anomalies à inclure, et **Filtrer les options d’anomalie** qui définit les anomalies à inclure dans l’alerte. Pour plus d’informations, consultez [Ajouter ou modifier des paramètres d’alerte](../how-tos/alerts.md#add-or-edit-alert-settings).


## <a name="next-steps"></a>Étapes suivantes

- [Ajouter vos données de métriques à Metrics Advisor](../how-tos/onboard-your-data.md)
    - [Gérer vos flux de données](../how-tos/manage-data-feeds.md)
    - [Connecter différentes sources de données](../data-feeds-from-different-sources.md)
- [Utiliser les bibliothèques de client ou les API REST pour personnaliser votre solution](./rest-api-and-client-library.md)
- [Configurer des métriques et affiner la configuration de la détection](../how-tos/configure-metrics.md)
