---
title: Affichage des données de trafic sur une carte Android | Microsoft Azure Maps
description: Dans cet article, vous allez apprendre à afficher des données de trafic sur une carte en utilisant le kit SDK Android Microsoft Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 012fdf6e35b0b0c27f8ad9afe10b5f70709fcc64
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113093555"
---
# <a name="show-traffic-data-on-the-map-android-sdk"></a>Affichage des données de trafic sur la carte (Android SDK)

Les données de flux et d’incidents sont deux types de données de trafic qui peut s’afficher sur la carte. Ce guide vous montre comment afficher ces deux types de données de trafic. Les données d’incidents se composent de données de type point et ligne qui peuvent indiquer des travaux, des routes barrées, des accidents, etc. Les données de flux présentent des métriques sur le flux du trafic routier.

## <a name="prerequisites"></a>Conditions préalables requises

Veillez à suivre la procédure du document [Démarrage rapide : Création d’une application Android](quick-android-map.md). Les blocs de code de cet article peuvent être insérés dans le gestionnaire d’événements `onReady` des cartes.

## <a name="show-traffic-on-the-map"></a>Afficher le trafic sur la carte

Deux types de données de trafic sont disponibles dans Azure Maps :

- Données d’incident : elles se composent de données de type point et ligne pour des éléments tels que les travaux, les fermetures de route et les accidents.
- Données de circulation : elles fournissent des métriques concernant la circulation sur les routes. Les données de circulation sont souvent utilisées pour colorer les routes. Les couleurs dépendent du volume de circulation qui entraîne un ralentissement par rapport à la limite de vitesse ou à une autre métrique. Quatre valeurs peuvent être passées dans l’option TrafficFlow de la carte.

    |Enum de flux | Description|
    | :-- | :-- |
    | `TrafficFlow.NONE` | N’affiche pas de données de trafic sur la carte |
    | `TrafficFlow.RELATIVE` | Affiche des données de trafic relatives à la vitesse dans des conditions de circulation routière fluide |
    | `TrafficFlow.RELATIVE_DELAY` | Affiche les zones qui sont plus lentes que le délai moyen attendu |
    | `TrafficFlow.ABSOLUTE` | Affiche la vitesse absolue de tous les véhicules circulant sur la route |

Le code suivant montre comment afficher les données de trafic sur la carte.

::: zone pivot="programming-language-java-android"

```java
//Show traffic on the map using the traffic options.
map.setTraffic(
    incidents(true),
    flow(TrafficFlow.RELATIVE)
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
map.setTraffic(
    incidents(true),
    flow(TrafficFlow.RELATIVE)
)
```

::: zone-end

La capture d’écran suivante montre le rendu, obtenu avec le code ci-dessus, des informations de trafic en temps réel sur la carte.

![Carte présentant des informations de trafic en temps réel](media/how-to-show-traffic-android/android-show-traffic.png)

## <a name="get-traffic-incident-details"></a>Récupération des détails des incidents de trafic

Les détails relatifs à un incident de trafic sont disponibles dans les propriétés de la fonctionnalité servant à afficher l’incident sur la carte. Les incidents de trafic sont ajoutés à la carte à l’aide du service de vignettes de vecteurs d’incident de trafic Azure Maps. Le format des données de ces vignettes de vecteurs est [documenté ici](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-incidents/vector-incident-tiles). Le code suivant ajoute un événement de clic à la carte et récupère la fonctionnalité d’incident de trafic sur laquelle l’utilisateur a cliqué. Il affiche un message toast contenant certains détails.

::: zone pivot="programming-language-java-android"

```java
//Show traffic information on the map.
map.setTraffic(
    incidents(true),
    flow(TrafficFlow.RELATIVE)
);

//Add a click event to the map.
map.events.add((OnFeatureClick) (features) -> {

    if (features != null && features.size() > 0) {
        Feature incident = features.get(0);

        //Ensure that the clicked feature is an traffic incident feature.
        if (incident.properties() != null && incident.hasProperty("incidentType")) {

            StringBuilder sb = new StringBuilder();
            String incidentType = incident.getStringProperty("incidentType");

            if (incidentType != null) {
                sb.append(incidentType);
            }

            if (sb.length() > 0) {
                sb.append("\n");
            }

            //If the road is closed, find out where it is closed from.
            if ("Road Closed".equals(incidentType)) {
                String from = incident.getStringProperty("from");

                if (from != null) {
                    sb.append(from);
                }
            } else {
                //Get the description of the traffic incident.
                String description = incident.getStringProperty("description");

                if (description != null) {
                    sb.append(description);
                }
            }

            String message = sb.toString();

            if (message.length() > 0) {
                Toast.makeText(this, message, Toast.LENGTH_LONG).show();
            }
        }
    }
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Show traffic information on the map.
map.setTraffic(
    incidents(true),
    flow(TrafficFlow.RELATIVE)
)

//Add a click event to the map.
map.events.add(OnFeatureClick { features: List<Feature>? ->
    if (features != null && features.size > 0) {
        val incident = features[0]

        //Ensure that the clicked feature is an traffic incident feature.
        if (incident.properties() != null && incident.hasProperty("incidentType")) {
            val sb = StringBuilder()
            val incidentType = incident.getStringProperty("incidentType")

            if (incidentType != null) {
                sb.append(incidentType)
            }

            if (sb.length > 0) {
                sb.append("\n")
            }

            //If the road is closed, find out where it is closed from.
            if ("Road Closed" == incidentType) {
                val from = incident.getStringProperty("from")
                if (from != null) {
                    sb.append(from)
                }
            } else { //Get the description of the traffic incident.
                val description = incident.getStringProperty("description")
                if (description != null) {
                    sb.append(description)
                }
            }

            val message = sb.toString()
            if (message.length > 0) {
                Toast.makeText(this, message, Toast.LENGTH_LONG).show()
            }
        }
    }
})
```

::: zone-end

La capture d’écran suivante montre le rendu, obtenu avec le code ci-dessus, des informations de trafic en temps réel sur la carte avec un message flottant indiquant les détails de l’incident.

![Carte présentant des informations de trafic en temps réel avec un message toast indiquant les détails de l’incident](media/how-to-show-traffic-android/android-traffic-details.png)

## <a name="filter-traffic-incidents"></a>Filtrer les incidents de circulation

Dans la plupart des grandes villes, au cours d’une journée ordinaire, il peut y avoir une multitude d’incidents de circulation. Toutefois, selon votre scénario, il peut être souhaitable de filtrer et d’afficher un sous-ensemble de ces incidents. Lors de la définition des options de circulation, il existe les options `incidentCategoryFilter` et `incidentMagnitudeFilter` qui acceptent en entrée un tableau de catégories d’incidents, d’énumérateurs de grandeur ou de valeurs de chaîne.

Le tableau suivant indique toutes les catégories d’incidents de circulation qui peuvent être utilisées dans l’option `incidentCategoryFilter`.

| Enum de catégorie | Valeur de chaîne | Description |
|--------------------|--------------|-------------|
| `IncidentCategory.UNKNOWN` | `"unknown"` | Incident qui ne correspond à aucune des catégories définies ou qui n’a pas encore été classifié. |
| `IncidentCategory.ACCIDENT` | `"accident"` | Accident de circulation. |
| `IncidentCategory.FOG` | `"fog"` | Brouillard qui réduit la visibilité, ce qui réduit probablement le flux de circulation et augmente éventuellement le risque d’accident. |
| `IncidentCategory.DANGEROUS_CONDITIONS` | `"dangerousConditions"` | Situation dangereuse sur la route, telle qu’un objet sur la chaussée. |
| `IncidentCategory.RAIN` | `"rain"` | Forte pluie susceptible de réduire la visibilité, de compliquer les conditions de conduite et d’augmenter éventuellement le risque d’accident. |
| `IncidentCategory.ICE` | `"ice"` | Conditions de verglas qui peuvent compliquer la conduite ou la rendre dangereuse. |
| `IncidentCategory.JAM` | `"jam"` | Embouteillage entraînant un ralentissement de la circulation. |
| `IncidentCategory.LANE_CLOSED` | `"laneClosed"` | Fermeture d’une voie sur la chaussée. |
| `IncidentCategory.ROAD_CLOSED` | `"roadClosed"` | Fermeture d’une route. |
| `IncidentCategory.ROAD_WORKS` | `"roadWorks"` | Travaux/construction dans cette zone. |
| `IncidentCategory.WIND` | `"wind"` | Vents violents qui peuvent compliquer la conduite des véhicules avec un profil de grande taille ou un centre de gravité élevé. |
| `IncidentCategory.FLOODING` | `"flooding"` | Chaussée inondée. |
| `IncidentCategory.DETOUR` | `"detour"` | Le trafic est dirigé vers une déviation. |
| `IncidentCategory.CLUSTER` | `"cluster"` | Cluster d’incidents de circulation de différentes catégories. Si vous effectuez un zoom sur la carte, le cluster se décompose en incidents individuels. |
| `IncidentCategory.BROKEN_DOWN_VEHICLE` | `"brokenDownVehicle"` | Véhicule en panne sur la chaussée ou au bord de la route. |

Le tableau suivant indique toutes les grandeurs d’incidents de circulation qui peuvent être utilisées dans l’option `incidentMagnitudeFilter`.

| Enum de grandeur | Valeur de chaîne | Description |
|--------------------|--------------|-------------|
| `IncidentMagnitude.UNKNOWN` | `"unknown"` | Incident dont la grandeur n’a pas encore été classifiée. |
| `IncidentMagnitude.MINOR` | `"minor"` | Problème de circulation mineur, souvent indiqué à titre d’information et qui a un impact minimal sur le flux de circulation. |
| `IncidentMagnitude.MODERATE` | `"moderate"` | Problème de circulation modéré qui a un impact sur le flux de circulation. |
| `IncidentMagnitude.MAJOR` | `"major"` |  Problème de circulation majeur qui a un impact significatif sur le flux de circulation. |

Le code suivant filtre les incidents de circulation, de sorte que seuls les embouteillages modérés et les incidents présentant un danger sont affichés sur la carte.

::: zone pivot="programming-language-java-android"

``` java
map.setTraffic(
    incidents(true),
    incidentMagnitudeFilter(new String[] { IncidentMagnitude.MODERATE }),
    incidentCategoryFilter(new String[] { IncidentCategory.DANGEROUS_CONDITIONS, IncidentCategory.JAM })              
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
map.setTraffic(
    incidents(true),
    incidentMagnitudeFilter(*arrayOf(IncidentMagnitude.MODERATE)),
    incidentCategoryFilter(
        *arrayOf(
            IncidentCategory.DANGEROUS_CONDITIONS,
            IncidentCategory.JAM
        )
    )
)
```

::: zone-end

La capture d’écran suivante montre une carte des embouteillages modérés et des incidents présentant un danger.

![Carte des embouteillages modérés et incidents présentant un danger.](media/how-to-show-traffic-android/android-traffic-incident-filters.jpg)

> [!NOTE]
> Plusieurs catégories peuvent être affectées à certains incidents de circulation. Si un incident a une catégorie qui correspond à une option transmise dans `incidentCategoryFilter`, il est affiché. La catégorie d’incident principale peut être différente des catégories spécifiées dans le filtre et afficher ainsi une icône différente.

## <a name="next-steps"></a>Étapes suivantes

Consultez les guides suivants pour savoir comment ajouter des données supplémentaires à votre carte :

> [!div class="nextstepaction"]
> [Ajouter une couche de mosaïques](how-to-add-tile-layer-android-map.md)
