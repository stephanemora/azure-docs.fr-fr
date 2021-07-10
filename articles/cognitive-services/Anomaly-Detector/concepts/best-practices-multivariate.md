---
title: Bonnes pratiques pour l’utilisation de l’API Détecteur d’anomalies (multivarié)
titleSuffix: Azure Cognitive Services
description: Bonnes pratiques pour l’utilisation de l’API Détecteur d’anomalies (multivarié) afin d’appliquer la détection d’anomalies à vos données de série chronologique.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: mbullwin
keywords: détection d’anomalie, Machine Learning, algorithmes
ms.openlocfilehash: 30778cf48efda57fc0d50964611d5616ce7a84d5
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110062251"
---
# <a name="multivariate-time-series-anomaly-detector-best-practices"></a>Bonnes pratiques concernant le Détecteur d’anomalies (multivarié) de série chronologique

Cet article fournit des conseils concernant les pratiques recommandées à suivre lors de l’utilisation des API Détecteur d’anomalies (multivarié).

## <a name="training-data"></a>Données de formation 

### <a name="data-schema"></a>Schéma de données
Pour utiliser les API multivariées Détecteur d’anomalies, vous devez d’abord entraîner vos propres modèles. Les données d’entraînement sont un ensemble de plusieurs séries chronologiques qui satisfont les exigences suivantes :

Chaque série chronologique doit être un fichier CSV comportant deux (et seulement deux) colonnes, avec **« timestamp »** et **« value »** (tout en minuscules) comme ligne d’en-tête. Les valeurs « timestamp » doivent être conformes à la norme ISO 8601 ; la colonne « value » peut contenir des entiers ou des nombres décimaux avec n’importe quel nombre de décimales. Par exemple :

|timestamp | value|
|-------|-------|
|2019-04-01T00:00:00Z| 5|
|2019-04-01T00:01:00Z| 3.6|
|2019-04-01T00:02:00Z| 4|
|`...`| `...` |

Chaque fichier CSV doit être nommé d’après une variable différente qui sera utilisée pour entraîner le modèle. Par exemple, « température.csv » et « humidité.csv ». Tous les fichiers CSV doivent être compressés dans un seul fichier zip ne contenant aucun sous-dossier. Le fichier zip peut porter le nom de votre choix. Le fichier zip doit être chargé dans le stockage Blob Azure. Une fois que vous avez généré l’[URL des signatures d’accès partagé (SAS) des objets blob](../../../storage/common/storage-sas-overview.md) pour le fichier zip, vous pouvez l’utiliser pour l’entraînement. Reportez-vous à ce document pour savoir comment générer des URL SAS à partir du stockage Blob Azure.

### <a name="data-quality"></a>Qualité des données
- Étant donné que le modèle apprend les patterns normaux à partir de données historiques, les données d’entraînement doivent **représenter l’état normal global du système**. Il est difficile pour le modèle d’apprendre ces types de patterns si les données d’entraînement sont pleines d’anomalies. 
-  Le modèle a des millions de paramètres, et il a besoin d’une quantité minimale de points de données pour apprendre un ensemble optimal de paramètres. La règle générale est que vous devez fournir **au moins 15 000 points de données par variable** pour entraîner correctement le modèle. Plus il y a de données, mieux c’est.
- En général, le **ratio des valeurs manquantes des données d’entraînement doit être inférieur à 20 %** . Une trop grande quantité de données peut finir par des valeurs renseignées automatiquement (généralement des segments droits ou des valeurs constantes) appris comme des patterns normaux. Cela peut entraîner la détection de points de données réels comme des anomalies. 

    Toutefois, il existe des cas où un ratio élevé est acceptable. Par exemple, si vous avez deux séries chronologiques dans un groupe utilisant le mode `Outer` pour aligner des horodatages. L’une d’elles a une précision d’une minute, l’autre a une précision horaire. Ensuite, la série chronologique horaire a, par nature, au moins 59 points de données manquants sur 60, soit 98,33 %. Dans ce cas, il est généralement bien de remplir la série chronologique horaire en utilisant la seule valeur disponible si elle ne fluctue pas trop.

## <a name="parameters"></a>Paramètres

### <a name="sliding-window"></a>Fenêtre glissante

La détection d’anomalie multivariée prend un segment de points de données de longueur `slidingWindow` comme entrée, et décide si le point de données suivant est une anomalie. Plus l’échantillon est long, plus la quantité de données prises en compte pour une décision sera élevée. Vous devez garder deux choses à l’esprit lors du choix d’une valeur appropriée pour `slidingWindow` : les propriétés des données d’entrée, et le compromis entre la durée d’inférence/entraînement et l’amélioration potentielle des performances. `slidingWindow` est un entier compris entre 28 et 2880. Vous pouvez décider du nombre de points de données utilisés comme entrées selon que vos données sont périodiques ou non, et en fonction du taux d’échantillonnage de vos données.

Lorsque vos données sont périodiques, vous pouvez inclure de un à trois cycles comme entrée, et lorsqu’elles sont échantillonnées à une fréquence élevée (précision élevée), comme les données à la minute ou à la seconde, vous pouvez sélectionner davantage de données comme entrée. Un autre problème est que les entrées plus longues peuvent entraîner une durée d’inférence/entraînement plus longue, et il n’y a aucune garantie que davantage de points d’entrée entraîneront des gains de performances. D’un autre côté, s’il y a trop peu de points de données, le modèle sera peut-être difficile à faire converger vers une solution optimale. Par exemple, il est difficile de détecter des anomalies lorsque les données d’entrée ne comptent que deux points.

### <a name="align-mode"></a>Mode d’alignement

Le paramètre `alignMode` sert à indiquer comment vous souhaitez aligner plusieurs séries chronologiques sur les horodatages. Cela est dû au fait que de nombreuses séries chronologiques ont des valeurs manquantes, et que nous devons les aligner sur les mêmes horodatages avant tout traitement supplémentaire. Il existe deux options pour ce paramètre : `inner join` et `outer join`. `inner join` signifie que nous signalerons les résultats de la détection sur les horodatages sur lesquels **chaque série chronologique** a une valeur, tandis que `outer join` signifie que nous signalerons les résultats de la détection sur les horodatages pour **n’importe quelle série chronologique** ayant une valeur.  **`alignMode` affecte également la séquence d’entrée du modèle.** Vous devez donc choisir un `alignMode` adapté à votre scénario, car les résultats peuvent être très différents.

Nous présentons ici un exemple pour expliquer différentes valeurs de `alignModel`.

#### <a name="series1"></a>Série 1

|timestamp | value|
----------| -----|
|`2020-11-01`| 1  
|`2020-11-02`| 2  
|`2020-11-04`| 4  
|`2020-11-05`| 5

#### <a name="series2"></a>Série 2

timestamp | value  
--------- | -
`2020-11-01`| 1  
`2020-11-02`| 2  
`2020-11-03`| 3  
`2020-11-04`| 4

#### <a name="inner-join-two-series"></a>Jointure interne - deux séries
  
timestamp | Série 1 | Série 2
----------| - | -
`2020-11-01`| 1 | 1
`2020-11-02`| 2 | 2
`2020-11-04`| 4 | 4

#### <a name="outer-join-two-series"></a>Jointure externe - deux séries

timestamp | Série 1 | Série 2
--------- | - | -
`2020-11-01`| 1 | 1
`2020-11-02`| 2 | 2
`2020-11-03`| N/D | 3
`2020-11-04`| 4 | 4
`2020-11-05`| 5 | N/D

### <a name="fill-not-available-na"></a>Remplissage non disponible (N/D)

Une fois les variables alignées sur l’horodatage par jointure externe, il peut y avoir une valeur `Not Available` (`NA`) dans certaines des variables. Vous pouvez spécifier la méthode pour remplir cette valeur N/D. Les autres options pour `fillNAMethod` sont `Linear`, `Previous`, `Subsequent`, `Zero` et `Fixed`.

| Option     | Méthode                                                                                           |
| ---------- | -------------------------------------------------------------------------------------------------|
| Linéaire     | Remplir les valeurs N/D par interpolation linéaire                                                           |
| Précédente   | Propager la dernière valeur valide pour remplir les vides. Exemple : `[1, 2, nan, 3, nan, 4]` -> `[1, 2, 2, 3, 3, 4]` |
| Subsequent | Utiliser la valeur valide suivante pour remplir les vides. Exemple : `[1, 2, nan, 3, nan, 4]` -> `[1, 2, 3, 3, 4, 4]`       |
| Zéro       | Remplir les valeurs N/D avec 0.                                                                           |
| Fixe      | Remplir les valeurs N/D avec une valeur valide spécifiée qui doit être fournie dans `paddingValue`.          |

## <a name="model-analysis"></a>Analyse de modèle

### <a name="training-latency"></a>Latence d’entraînement

L’entraînement avec détection d’anomalie multivariée peut prendre du temps, en particulier lorsqu’une quantité élevée d’horodatages est utilisé pour l’entraînement. Par conséquent, nous autorisons une partie du processus d’entraînement à être asynchrone. En règle générale, les utilisateurs envoient une tâche d’entraînement par le biais de l’API d’entraînement de modèle, puis ils récupèrent l’état du modèle par le biais de `Get Multivariate Model API`. Nous allons ici illustrer comment extraire la durée restante avant la fin de l’entraînement. Dans la réponse de l’API d’obtention de modèle multivarié figure un élément nommé `diagnosticsInfo`. Dans cet élément figure un élément `modelState`. Pour calculer la durée restante, nous devons utiliser `epochIds` et `latenciesInSeconds`. Une époque représente un cycle complet des données d’entraînement. Toutes les dix époques, nous allons générer des informations d’état. Au total, nous allons effectuer un entraînement pour 100 époques. La latence indique la durée d’une époque. Avec ces informations, nous pouvons déduire combien il reste de temps afin la fin de l’entraînement du modèle.

### <a name="model-performance"></a>Performances du modèle

La détection d’anomalie multivariée est un modèle non supervisé, et la meilleure façon de l’évaluer consiste à vérifier les résultats d’anomalies manuellement. Dans la réponse de l’API d’obtention de modèle multivarié figurent des informations de base qui nous permettent d’analyser les performances du modèle. Dans l’élément `modelState` retourné par cette API, nous pouvons utiliser `trainLosses` et `validationLosses` pour évaluer si le modèle a été entraîné comme prévu. Dans la plupart des cas, les deux pertes diminuent progressivement. Nous pouvons également analyser les performances du modèle à l’aide des informations qui se trouvent dans `variableStates`. La liste des états de variables est classée par `filledNARatio` dans l’ordre décroissant. Plus la valeur est élevée, moins les performances sont bonnes. En règle générale, il convient de réduire le plus possible cette valeur `NA ratio`. `NA` peut être dû à des valeurs manquantes ou à des variables non alignées d’un point de vue de l’horodatage.

## <a name="next-steps"></a>Étapes suivantes

- [Démarrages rapides](../quickstarts/client-libraries-multivariate.md).
- [Découvrir les algorithmes sous-jacents utilisés par le détecteur d’anomalies multivarié](https://arxiv.org/abs/2009.02040)
