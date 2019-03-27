---
title: Meilleures pratiques lors de l’utilisation de l’API de détecteur d’anomalie
description: En savoir plus sur les meilleures pratiques lors de la détection des anomalies avec l’API de détecteur d’anomalies.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 467ac4e475a1c23e25b62c76cfbc959e7ed49465
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58484031"
---
# <a name="best-practices-for-using-the-anomaly-detector-api"></a>Meilleures pratiques pour l’utilisation de l’API de détecteur d’anomalie

L’API de détecteur d’anomalie est un service de détection d’anomalie sans état. La précision et les performances de ses résultats peuvent être affectées par :

* Comment préparer vos données de série chronologique.
* Les paramètres de l’API de détecteur d’anomalies qui ont été utilisés.
* Le nombre de points de données dans votre demande d’API. 

Utilisez cet article pour en savoir plus sur les meilleures pratiques pour l’utilisation de l’API d’obtenir de meilleurs résultats pour vos données. 

## <a name="data-preparation"></a>Préparation des données

L’API de détecteur d’anomalie accepte la série chronologique mis en forme des données dans un objet de demande JSON. Une série chronologique peut être n’importe quel enregistrées au fil du temps dans l’ordre séquentiel des données numériques. Vous pouvez envoyer des fenêtres de vos données de série chronologique pour le point de terminaison d’API de détecteur d’anomalies pour améliorer les performances de l’API. Le nombre minimal de points de données que vous pouvez envoyer est 12, et la valeur maximale est 8640 points. 

Les points de données envoyées à l’API de détecteur d’anomalie doivent avoir un horodatage valide de temps universel coordonné (UTC) et une valeur numérique. 

```json
{
    "granularity": "daily",
    "series": [
      {
        "timestamp": "2018-03-01T00:00:00Z",
        "value": 32858923
      },
      {
        "timestamp": "2018-03-02T00:00:00Z",
        "value": 29615278
      },
    ]
}
```

### <a name="missing-data-points"></a>Points de données manquants

Les points de données manquants sont courantes dans les jeux de données de série heure uniformément distribués, en particulier ceux disposant d’un niveau de granularité fine (un intervalle d’échantillonnage petites. Par exemple, les données échantillonnées quelques minutes). Manque de moins de 10 % du nombre prévu de points dans vos données ne doit pas avoir un impact négatif sur vos résultats de la détection. Pensez à des intervalles dans vos données selon ses caractéristiques telles que la substitution des points de données à partir d’une période précédente, une interpolation linéaire ou une moyenne mobile.

### <a name="aggregate-distributed-data"></a>Agrégation des données distribuées

L’API de détecteur d’anomalie fonctionne mieux sur une distribution égale MTS. Si vos données sont réparties au hasard, vous devez l’agrégation d’une unité de temps, par exemple à la minute, toutes les heures ou tous les jour par exemple.

## <a name="anomaly-detection-on-data-with-seasonal-patterns"></a>Détection d’anomalies sur les données avec des schémas saisonniers

Si vous savez que vos données de série chronologique ont un modèle saisonnier (celui qui se produit à intervalles réguliers), vous pouvez améliorer la précision et le temps de réponse d’API. 

En spécifiant un `period` lorsque vous construisez votre demande JSON peut réduire la latence de la détection d’anomalie jusqu'à 50 %. Le `period` est un entier qui spécifie à peu près comment de nombreux points de données de la série chronologique nécessaires pour répéter un modèle. Par exemple, une série chronologique avec un point de données par jour aurait un `period` comme `7`, et une série chronologique avec un point par heure (avec le même modèle hebdomadaire) aurait un `period` de `7*24`. Si vous ne savez pas les modèles de vos données, vous n’êtes pas obligé de spécifier ce paramètre.

Pour de meilleurs résultats, fournissez 4 `period`de valeur de point de données, ainsi que sur une autre. Par exemple, les données toutes les heures avec un modèle hebdomadaire comme décrit ci-dessus doivent fournir 673 points de données dans le corps de la demande (`7 * 24 * 4 + 1`).

### <a name="sampling-data-for-real-time-monitoring"></a>Données d’échantillonnage pour l’analyse en temps réel

Si vos données de diffusion en continu sont échantillonnées à un court intervalle (par exemple, secondes ou minutes), l’envoi le nombre recommandé de points de données peut dépasser maximale nombre autorisé (8640 points de données) de l’API détecteur d’anomalie. Si vos données montrent un modèle saisonnier stable, envisagez d’envoyer un échantillon de vos données de série chronologique à un intervalle de temps plus volumineux, tels que les heures. Échantillonnage des données de cette façon peut améliorer également sensiblement le temps de réponse d’API. 

## <a name="next-steps"></a>Étapes suivantes

* [Qu’est l’API de détecteur d’anomalie ?](../overview.md)
* [Démarrage rapide : Détecter les anomalies dans vos données de série chronologique à l’aide de l’API REST de détecteur d’anomalie](../quickstarts/detect-data-anomalies-csharp.md)
