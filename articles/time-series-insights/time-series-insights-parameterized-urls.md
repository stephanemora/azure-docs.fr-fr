---
title: Partager des vues Azure Time Series Insights personnalisées avec des URL paramétrables | Microsoft Docs
description: Cet article explique comment développer des URL paramétrables dans Azure Time Series Insights, afin qu’une vue client puisse être facilement partagée.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.topic: conceptual
ms.workload: big-data
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: e70eb7ae73e88b37e649d519d0d0428554dd4ab3
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65467515"
---
# <a name="share-a-custom-view-using-a-parameterized-url"></a>Partager une vue personnalisée à l’aide d’une URL paramétrable

Pour partager une vue personnalisée dans l’Explorateur Time Series Insights, vous pouvez créer par programmation une URL paramétrable de la vue personnalisée.

L’Explorateur Time Series Insights prend en charge les paramètres de requête d’URL pour spécifier des vues dans l’expérience directement à partir de l’URL. Par exemple, en utilisant l’URL uniquement, vous pouvez spécifier un environnement cible, un prédicat de recherche et la durée désirée. Lorsqu’un utilisateur clique sur l’URL personnalisée, l’interface fournit directement un lien vers la ressource dans le portail Time Series Insights. Les stratégies d’accès aux données s’appliquent.

> [!TIP]
> * Afficher la version gratuite [Time Series Insights démonstration](https://insights.timeseries.azure.com/samples).
> * Lire l’accompagnement [Explorateur Time Series Insights](./time-series-insights-explorer.md) documentation.

## <a name="environment-id"></a>ID de l’environnement

Le paramètre `environmentId=<guid>` spécifie l’ID de l’environnement cible. C’est un composant de l’accès aux données nom de domaine complet, et vous pouvez le trouver dans l’angle supérieur droit de la vue d’ensemble de l’environnement dans le portail Azure. Cela correspond à tous les éléments précédant `env.timeseries.azure.com`.

Exemple de paramètre d’ID de l’environnement : `?environmentId=10000000-0000-0000-0000-100000000108`.

## <a name="time"></a>Temps

Vous pouvez spécifier des valeurs de temps absolues ou relatives avec une URL paramétrable.

### <a name="absolute-time-values"></a>Valeurs de temps absolues

Pour les valeurs de temps absolues, utilisez les paramètres `from=<integer>` et `to=<integer>`.

* `from=<integer>` est une valeur JavaScript, en millisecondes, qui définit l’heure de début de la période de recherche.
* `to=<integer>` est une valeur JavaScript, en millisecondes, qui définit l’heure de fin de la période de recherche.

Pour identifier le nombre de millisecondes JavaScript d’une date, consultez la section relative au [convertisseur d’horodatage Epoch et Unix](https://www.freeformatter.com/epoch-timestamp-to-date-converter.html).

### <a name="relative-time-values"></a>Valeurs de temps relatives

Dans le cas d’une valeur de temps relative, utilisez l’élément `relativeMillis=<value>`, où la *valeur* est exprimée en millisecondes JavaScript à partir des données les plus récentes sur le serveur principal.

Par exemple, l’élément `&relativeMillis=3600000` affiche les dernières 60 minutes de données.

Valeurs acceptées correspondent à l’Explorateur Time Series Insights **accélérer le délai** menu et incluent :

* `1800000` (30 dernières minutes)
* `3600000` (Dernières 60 minutes)
* `10800000` (3 dernières heures)
* `21600000` (6 dernières heures)
* `43200000` (12 dernières heures)
* `86400000` (Dernières 24 heures)
* `604800000` (7 derniers jours)
* `2592000000` (30 dernières heures)

### <a name="optional-parameters"></a>Paramètres facultatifs

Le `timeSeriesDefinitions=<collection of term objects>` paramètre spécifie les conditions d’une vue Time Series Insights :

| Paramètre | Élément d’URL | Description  |
| --- | --- | --- |
| **name** | `\<string>` | nom du *terme* ; |
| **splitBy** | `\<string>` | nom de colonne pour le *fractionnement* ; |
| **measureName** | `\<string>` | nom de colonne de *mesure* ; |
| **predicate** | `\<string>` | clause *where* pour le filtrage côté serveur. |
| **useSum** | `true` | Un paramètre facultatif qui spécifie l’utilisation de sum pour votre mesure. </br>  Notez que si `Events` est la mesure sélectionnée, nombre est sélectionné par défaut.  </br>  Si `Events` est ne pas sélectionnée, la moyenne est sélectionnée par défaut. |

* Le `multiChartStack=<true/false>` paire clé-valeur permet l’empilement dans le graphique.
* Le `multiChartSameScale=<true/false>` paire clé-valeur permet à la même échelle d’axe des ordonnées entre les termes au sein d’un paramètre facultatif.  
* Le `timeBucketUnit=<Unit>&timeBucketSize=<integer>` vous permet d’ajuster le curseur intervalle pour fournir plus précis ou plus lisse, plus agrégées vue du graphique.  
* Le `timezoneOffset=<integer>` paramètre vous permet de définir le fuseau horaire pour le graphique à afficher en tant qu’offset UTC.

| Ou des paires | Description  |
| --- | --- |
| `multiChartStack=false` | `true` est activé par défaut donc passez `false` à la pile. |
| `multiChartStack=false&multiChartSameScale=true` | L’empilement doit être activé pour pouvoir utiliser la même échelle de l’axe des ordonnées entre les termes.  Il a `false` par défaut, par conséquent, en passant 'true' active cette fonctionnalité. |
| `timeBucketUnit=<Unit>&timeBucketSize=<integer>` | Unités = jours, heures, minutes, secondes, millisecondes.  Mettez toujours les unités en majuscules. </br> Définissez le nombre d’unités en passant l’entier souhaité pour timeBucketSize.  Notez que vous simplifiez jusqu'à 7 jours.  |
| `timezoneOffset=-<integer>` | L’entier est toujours en millisecondes. </br> Notez que cette fonctionnalité est légèrement différente de celle que nous proposons dans l’Explorateur TSI, dans lequel nous vous permettons de choisir l’heure locale (l’heure du navigateur) ou l’heure UTC. |

### <a name="examples"></a>Exemples

Pour ajouter des définitions time series à un environnement TSI comme un paramètre d’URL, ajoutez :

```plaintext
&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},
{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

Utilisez les définitions exemple time series pour :

* L’ID de l’environnement
* Cours des 60 dernières minutes de données
* Les termes (F1PressureID, F2TempStation et F3VibrationPL) qui comprennent les paramètres facultatifs

Vous pouvez construire l’URL paramétrable suivante pour obtenir une vue :

```plaintext
https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

> [!TIP]
> Voir l’Explorateur live [à l’aide de l’URL](https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]).

L’URL ci-dessus décrit et génère la vue Explorateur TSI :

[![Termes de temps série Insights explorer](media/parameterized-url/url1.png)](media/parameterized-url/url1.png#lightbox)

Vue d’ensemble (y compris le graphique) :

[![Vue graphique](media/parameterized-url/url2.png)](media/parameterized-url/url2.png#lightbox)

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [à l’aide de données de requête C# ](time-series-insights-query-data-csharp.md).

* En savoir plus sur la [temps série Insights Explorer](./time-series-insights-explorer.md).
