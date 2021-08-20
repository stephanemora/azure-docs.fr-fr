---
title: Paramètres d'entrée MVAD
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: quying
manager: tonyxin
ms.service: cognitive-services
ms.topic: include
ms.date: 7/1/2021
ms.author: yingqunpku
ms.openlocfilehash: 5b4cea9dea4bb8ee6d1f32ec0e21fd87eb55da95
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2021
ms.locfileid: "113215225"
---
### <a name="input-parameters"></a>Paramètres d'entrée

#### <a name="required-parameters"></a>Paramètres obligatoires

Ces trois paramètres sont nécessaires dans les requêtes d’API d’entraînement et d’inférence :

* `source` : lien vers votre fichier zip situé dans Stockage Blob Azure avec signatures d’accès partagé (SAS).
* `startTime` : heure de début des données utilisées pour l’entraînement ou l’inférence. Si elle est antérieure à l’horodatage réel le plus ancien dans les données, l’horodatage réel le plus ancien sera utilisé comme point de départ.
* `endTime` : heure de fin des données utilisées pour l’entraînement ou l’inférence, qui doit être postérieure ou égale à `startTime`. Si `endTime` est postérieur à l’horodatage réel le plus récent dans les données, l’horodatage réel le plus récent sera utilisé comme point de fin. Si `endTime` est égal à `startTime`, cela signifie qu’il s’agit de l’inférence d’un point de données unique, qui est souvent utilisée dans les scénarios de streaming.

#### <a name="optional-parameters-for-training-api"></a>Paramètres facultatifs pour l’API d’entraînement

Les autres paramètres de l’API d’entraînement sont facultatifs :

* `slidingWindow` : nombre de points de données utilisés pour déterminer les anomalies. Entier compris entre 28 et 2880. La valeur par défaut est 300. Si `slidingWindow` est `k` pour l’entraînement du modèle, au moins `k` points doivent être accessibles à partir du fichier source pendant l’inférence pour obtenir des résultats valides.

    La détection d'anomalie multivariée prend un segment de points de données pour décider si le point de données suivant est une anomalie. La longueur du segment est `slidingWindow`.
    Gardez deux choses à l’esprit lorsque vous choisissez une valeur `slidingWindow` :
    1. Les propriétés de vos données (sont-elles périodiques, et quel est le taux d’échantillonnage ?). Lorsque vos données sont périodiques, vous pouvez affecter une longueur de un à trois cycles à `slidingWindow`. Lorsque vos données sont à fréquence élevée (haute précision), par exemple au niveau de la minute ou de la seconde, vous pouvez affecter une valeur supérieure à `slidingWindow`.
    1. Le compromis entre le temps d’entraînement/d’inférence et l’impact potentiel sur les performances. Une valeur plus élevée de `slidingWindow` peut provoquer un temps d’entraînement/d’inférence plus long. Il n’existe **aucune garantie** qu’une valeur plus élevée de `slidingWindow` engendrera un gain de précision. Une `slidingWindow` peu élevée risque de rendre le modèle difficile à converger vers une solution optimale. Par exemple, il est difficile de détecter des anomalies lorsque `slidingWindow` ne compte que deux points.

* `alignMode` : comment aligner plusieurs variables (série chronologique) sur des horodatages. Il existe deux options pour ce paramètre : `Inner` et `Outer`. La valeur par défaut est `Outer`.

    Ce paramètre est essentiel lorsqu’il y a un mauvais alignement entre les séquences d’horodatage des variables. Le modèle doit aligner les variables sur la même séquence d’horodatage avant tout traitement ultérieur.

    `Inner` signifie que le modèle doit signaler les résultats de la détection uniquement sur les horodatages sur lesquels **chaque variable** a une valeur, c’est-à-dire l’intersection de toutes les variables. `Outer` signifie que le modèle doit signaler les résultats de la détection sur les horodatages sur lesquels **une variable quelconque** a une valeur, c’est-à-dire l’union de toutes les variables.

    Voici un exemple illustrant différentes valeurs de `alignModel`.

    *Variable-1*

    |timestamp | value|
    ----------| -----|
    |2020-11-01| 1  
    |2020-11-02| 2  
    |2020-11-04| 4  
    |05-11-2020| 5

    *Variable-2*

    timestamp | value  
    --------- | -
    2020-11-01| 1  
    2020-11-02| 2  
    2020-11-03| 3  
    2020-11-04| 4

    *Jointure `Inner` de deux variables*

    timestamp | Variable-1 | Variable-2
    ----------| - | -
    2020-11-01| 1 | 1
    2020-11-02| 2 | 2
    2020-11-04| 4 | 4

    *Jointure `Outer` de deux variables*

    timestamp | Variable-1 | Variable-2
    --------- | - | -
    2020-11-01| 1 | 1
    2020-11-02| 2 | 2
    2020-11-03| `nan` | 3
    2020-11-04| 4 | 4
    05-11-2020| 5 | `nan`

* `fillNAMethod` : comment renseigner `nan` dans la table fusionnée. Il peut y avoir des valeurs manquantes dans la table fusionnée, et elles doivent être gérées correctement. Nous fournissons plusieurs méthodes pour les renseigner. Les options sont `Linear`, `Previous`, `Subsequent`, `Zero` et `Fixed`, et la valeur par défaut est `Linear`.

    | Option     | Méthode                                                                                           |
    | ---------- | -------------------------------------------------------------------------------------------------|
    | `Linear`     | Renseigner les valeurs `nan` par interpolation linéaire                                                           |
    | `Previous`   | Propager la dernière valeur valide pour remplir les vides. Exemple : `[1, 2, nan, 3, nan, 4]` -> `[1, 2, 2, 3, 3, 4]` |
    | `Subsequent` | Utiliser la valeur valide suivante pour remplir les vides. Exemple : `[1, 2, nan, 3, nan, 4]` -> `[1, 2, 3, 3, 4, 4]`       |
    | `Zero`       | Renseigner les valeurs `nan` avec 0.                                                                           |
    | `Fixed`      | Renseigner les valeurs `nan` avec une valeur valide spécifiée qui doit être fournie dans `paddingValue`.          |

* `paddingValue` : la valeur de remplissage est utilisée pour renseigner `nan` lorsque `fillNAMethod` est `Fixed` et doit être fournie dans ce cas. Dans les autres cas, elle est facultative.

* `displayName` : il s’agit d’un paramètre facultatif servant à identifier des modèles. Vous pouvez par exemple l’utiliser pour marquer des paramètres, des sources de données et toute autre métadonnée relative au modèle et à ses données d’entrée. La valeur par défaut est une chaîne vide.
