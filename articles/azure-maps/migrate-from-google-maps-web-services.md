---
title: 'Tutoriel : Migrer des services web à partir de Google Maps | Microsoft Azure Maps'
description: Guide pratique pour migrer des services web de Google Maps vers Microsoft Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 51c00524c781d9af58f60b36aa3baeb079c6eafa
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2020
ms.locfileid: "75910754"
---
# <a name="migrate-web-service-from-google-maps"></a>Migrer un service web à partir de Google Maps

Azure Maps et Google Maps donnent tous deux accès à des API spatiales via des services web REST. Les interfaces API de ces plateformes offrent des fonctionnalités similaires mais utilisent des conventions d’affectation de noms et des objets de réponse différents.

Le tableau suivant présente les API du service Azure Maps qui offrent des fonctionnalités similaires à celles des API du service Google Maps répertoriées.

| API du service Google Maps | API du service Azure Maps                                                                      |
|-------------------------|---------------------------------------------------------------------------------------------|
| Directions              | [Itinéraire](https://docs.microsoft.com/rest/api/maps/route)                               |
| Matrice des distances         | [Matrice d’itinéraire](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview) |
| Géocodage               | [action](https://docs.microsoft.com/rest/api/maps/search)                             |
| Recherche de lieux           | [action](https://docs.microsoft.com/rest/api/maps/search)                             |
| Autocomplétion des lieux      | [action](https://docs.microsoft.com/rest/api/maps/search)                             |
| Carte statique              | [Render](https://docs.microsoft.com/rest/api/maps/render/getmapimage)                 |
| Time Zone (Fuseau horaire)               | [Fuseau horaire](https://docs.microsoft.com/rest/api/maps/timezone)                        |

Actuellement, les API de service suivantes ne sont pas disponibles dans Azure Maps :

- Elevation
- Géolocalisation
- Détails et photos des lieux. Les numéros de téléphone et l’URL du site web sont disponibles dans l'API de recherche Azure Maps.
- URL de carte
- Routes : les données sur les limitations de vitesse sont disponibles par le biais des API de géocodage de routes et de géocodage inverse dans Azure Maps.
- Affichage des rues statiques

Azure Maps offre plusieurs autres services web REST qui peuvent être utiles :

- [Opérations spatiales](https://docs.microsoft.com/rest/api/maps/spatial) : déchargez vers un service les calculs et les opérations spatiales complexes, par exemple le geofencing.
- [Trafic](https://docs.microsoft.com/rest/api/maps/traffic) : accédez en temps réel aux données sur le trafic et les incidents.

## <a name="geocoding-addresses"></a>Géocodage d’adresses

Le géocodage est le processus de conversion d’une adresse (par exemple « 1 Microsoft way, Redmond, WA ») en une coordonnée (par exemple longitude : -122.1298, latitude : 47.64005). Les coordonnées sont alors souvent utilisées pour positionner un marqueur sur une carte ou centrer une carte.

Azure Maps propose plusieurs méthodes pour le géocodage des adresses :

- [**Géocodage d’adresses en forme libre**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) : Spécifiez une seule chaîne d’adresse (par exemple « 1 Microsoft way, Redmond, WA ») et traitez aussitôt la requête. Ceci est recommandé si vous avez besoin de géocoder rapidement des adresses individuelles.
- [**Géocodage d’adresses structurées**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressstructured) : Précisez les parties d’une même adresse, par exemple le nom de la rue, la ville, le pays et le code postal, et traitez aussitôt la requête. Ceci est recommandé si vous avez besoin de géocoder rapidement des adresses individuelles et que les données sont déjà analysées dans leurs parties d’adresse individuelles.
- [**Géocodage des adresses par lots**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatchpreview) : Créez une requête contenant jusqu'à 10 000 adresses, puis traitez-les sur une certaine période. Toutes les adresses seront géocodées en parallèle sur le serveur et, une fois l’opération terminée, l’ensemble complet des résultats pourra être téléchargé. Ceci est recommandé pour le géocodage de grands groupes de données.
- [**Recherche approximative**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) : Cette API combine le géocodage des adresses et la recherche de points d’intérêt. Cette API prend en compte une chaîne de forme libre qui peut être une adresse, un lieu, un point de repère, un point d'intérêt ou une catégorie de point d'intérêt, puis traite immédiatement la requête. Cette API est recommandée pour les applications où les utilisateurs peuvent rechercher des adresses ou des points d'intérêt à partir d'une même zone de texte.
- [**Recherche approximative par lot** ](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview) : Créez une requête contenant jusqu'à 10 000 adresses, lieux, points de repère ou centres d'intérêt, puis traitez-les sur une certaine période. Toutes les données seront traitées en parallèle sur le serveur et, une fois l’opération terminée, l’ensemble complet des résultats pourra être téléchargé.

Le tableau suivant référence de manière croisée les paramètres de l’API Google Maps et les paramètres d’API comparables dans Azure Maps.

| Paramètre d’API Google Maps | Paramètre d’API Azure Maps comparable  |
|---------------------------|--------------------------------------|
| `address`                   | `query`                            |
| `bounds`                    | `topLeft` et `btmRight`           |
| `components`                | `streetNumber`<br/>`streetName`<br/>`crossStreet`<br/>`postalCode`<br/>`municipality` - ville<br/>`municipalitySubdivision` – voisinage, petite / grande ville<br/>`countrySubdivision` - état ou province<br/>`countrySecondarySubdivision` - comté<br/>`countryTertiarySubdivision` - district<br/>`countryCode` - indicatif de pays à deux lettres |
| `key`                       | `subscription-key` : voir également la documentation [Authentification avec Azure Maps](azure-maps-authentication.md). |
| `language`                  | `language` : voir la documentation [Langues prises en charge](supported-languages.md).  |
| `region`                    | `countrySet`                       |

Vous trouverez [ici](how-to-search-for-address.md) un exemple d’utilisation du service de recherche. Veillez à consulter la documentation [Bonnes pratiques pour la recherche](how-to-use-best-practices-for-search.md).

> [!TIP]
> Les API de géocodage d’adresses en forme libre et de recherche approximative peuvent être utilisées en mode autocomplétion en ajoutant `&amp;typeahead=true` à l’URL de la requête. Cela indiquera au serveur que le texte entré est probablement partiel et passera en mode prédictif.

## <a name="reverse-geocode-a-coordinate"></a>Géocodage inverse d’une coordonnée

Le géocodage inverse est le processus de conversion de coordonnées géographiques (par exemple longitude : -122.1298, la latitude : 47.64005) dans son adresse approximative (par exemple « 1 Microsoft way, Redmond, WA »).

Azure Maps propose plusieurs méthodes de géocodage inverse :

- [**Géocodeur inverse d’adresse**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) : Spécifiez une seule coordonnée géographique pour obtenir son adresse approximative et traitez aussitôt la requête.
- [**Géocodeur inverse d’intersection**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet) : Spécifiez une seule coordonnée géographique pour obtenir des informations sur les intersections proches (par exemple, 1re et rue principale) et traitez aussitôt la requête.
- [**Géocodeur inverse d’adresses par lots**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressreversebatchpreview) : Créez une requête contenant jusqu'à 10 000 coordonnées, puis traitez-les sur une certaine période. Toutes les données seront traitées en parallèle sur le serveur et, une fois l’opération terminée, l’ensemble complet des résultats pourra être téléchargé.

Le tableau suivant référence de manière croisée les paramètres de l’API Google Maps et les paramètres d’API comparables dans Azure Maps.

| Paramètre d’API Google Maps   | Paramètre d’API Azure Maps comparable   |
|-----------------------------|---------------------------------------|
| `key`                       | `subscription-key` : voir également la documentation [Authentification avec Azure Maps](azure-maps-authentication.md). |
| `language`                  | `language` : voir la documentation [Langues prises en charge](supported-languages.md).  |
| `latlng`                    | `query`  |
| `location_type`             | *N/A*     |
| `result_type`               | `entityType`    |

Veillez à consulter la documentation [Bonnes pratiques pour la recherche](how-to-use-best-practices-for-search.md).

L’API de géocodage inverse d'Azure Maps possède quelques fonctionnalités supplémentaires non disponibles dans Google Maps et qui pourraient être utiles à intégrer lors de la migration de votre application :

- Récupérer les données de limitations de vitesse.
- Récupérer des informations sur la nature des routes : route locale, artère, accès limité, rampe, etc.
- Côté de la rue de la coordonnée spécifiée.

## <a name="search-for-points-of-interest"></a>Rechercher des points d’intérêt

Les données de points d'intérêt peuvent être recherchées dans Google Maps en utilisant son API de recherche de lieux. Cette API offre trois façons différentes de rechercher des points d'intérêt :

- **Trouver un lieu à partir d’un texte :** Recherche d’un point d'intérêt à partir de son nom, de son adresse ou de son numéro de téléphone.
- **Recherche à proximité** : Recherche des points d'intérêt se trouvant à une certaine distance d'un endroit.
- **Recherche de texte :** Recherche de lieux à l’aide d’un texte libre comprenant des informations sur des points d’intérêt et des emplacements. Par exemple, « pizza à New York » ou « restaurants près de la rue principale ».

Azure Maps fournit plusieurs API de recherche de points d'intérêt :

- [**Recherche de points d'intérêt**](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi) : Rechercher des points d’intérêt par nom. Par exemple, « Starbucks ».
- [**Recherche de catégories de points d'intérêt**](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory) : Rechercher des points d’intérêt par catégorie. Par exemple, « restaurant ».
- [**Recherche à proximité**](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby) : Recherche des points d'intérêt se trouvant à une certaine distance d'un endroit.
- [**Recherche approximative**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) : Cette API combine le géocodage des adresses et la recherche de points d’intérêt. Cette API prend en compte une chaîne de forme libre qui peut être une adresse, un lieu, un point de repère, un point d'intérêt ou une catégorie de point d'intérêt, puis traite immédiatement la requête. Cette API est recommandée pour les applications où les utilisateurs peuvent rechercher des adresses ou des points d'intérêt à partir d'une même zone de texte.
- [**Rechercher dans la géométrie**](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry) : Recherche de points d'intérêt dans une géométrie spécifiée (polygone).
- [**Recherche le long d’un itinéraire**](https://docs.microsoft.com/rest/api/maps/search/postsearchalongroute) : Recherche de points d'intérêt qui se trouvent le long d'un itinéraire spécifié.
- [**Recherche approximative par lot** ](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview) : Créez une requête contenant jusqu'à 10 000 adresses, lieux, points de repère ou centres d'intérêt, puis traitez-les sur une certaine période. Toutes les données seront traitées en parallèle sur le serveur et, une fois l’opération terminée, l’ensemble complet des résultats pourra être téléchargé.

Actuellement, Azure Maps ne propose pas d'API comparable à l'API de recherche de texte de Google Maps.

> [!TIP]
> Les API de recherche de points d’intérêt, de catégorie de points d’intérêt et de recherche approximative peuvent être utilisées en mode autocomplétion en ajoutant `&amp;typeahead=true` à l’URL de la requête. Cela indiquera au serveur que le texte entré est probablement partiel et passera en mode prédictif.

Veillez à consulter la documentation [Bonnes pratiques pour la recherche](how-to-use-best-practices-for-search.md).

### <a name="find-place-from-text"></a>Trouver un lieu à partir d’un texte

Pour rechercher des points d’intérêt par nom ou par adresse, vous pouvez utiliser les API Azure Maps [Recherche de points d'intérêt](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi) et [Recherche approximative](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy).

Le tableau suivant référence de manière croisée les paramètres de l’API Google Maps et les paramètres d’API comparables dans Azure Maps.

| Paramètre d’API Google Maps | Paramètre d’API Azure Maps comparable |
|---------------------------|-------------------------------------|
| `fields`                  | *N/A*                               |
| `input`                   | `query`                             |
| `inputtype`               | *N/A*                               |
| `key`                     | `subscription-key` : voir également la documentation [Authentification avec Azure Maps](azure-maps-authentication.md). |
| `language`                | `language` : voir la documentation [Langues prises en charge](supported-languages.md).  |
| `locationbias`            | `lat`, `lon` et `radius`<br/>`topLeft` et `btmRight`<br/>`countrySet`  |

### <a name="nearby-search"></a>Recherche à proximité

Les points d'intérêt à proximité peuvent être récupérés dans Azure Maps en utilisant l'API [Recherche à proximité](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby).

Le tableau suivant référence de manière croisée les paramètres de l’API Google Maps et les paramètres d’API comparables dans Azure Maps.

| Paramètre d’API Google Maps | Paramètre d’API Azure Maps comparable  |
|---------------------------|--------------------------------------|
| `key`                       | `subscription-key` : voir également la documentation [Authentification avec Azure Maps](azure-maps-authentication.md). |
| `keyword`                   | `categorySet` et `brandSet`        |
| `language`                  | `language` : voir la documentation [Langues prises en charge](supported-languages.md).  |
| `location`                  | `lat` et `lon`                     |
| `maxprice`                  | *N/A*                               |
| `minprice`                  | *N/A*                               |
| `name`                      | `categorySet` et `brandSet`        |
| `opennow`                   | *N/A*                               |
| `pagetoken`                 | `ofs` et `limit`                   |
| `radius`                    | `radius`                            |
| `rankby`                    | *N/A*                               |
| `type`                      | `categorySet –` Voir la documentation [Catégories de recherche prise en charge](supported-search-categories.md).   |

## <a name="calculate-routes-and-directions"></a>Calculer des itinéraires et des directions

Azure Maps permet de calculer des itinéraires et des directions. Azure Maps offre de nombreuses fonctionnalités similaires à celles du service de routage Google Maps, par exemple :

- heures d’arrivée et de départ.
- itinéraires en temps réel et selon les prédictions de circulation.
- différents modes de transport ; voiture, marche, vélo.

> [!NOTE]
> Pour Azure Maps, tous les points de cheminement doivent être des coordonnées. Les adresses devront d’abord être géocodées.

Le service de routage Azure Maps fournit les API suivantes pour le calcul des itinéraires :

- [**Calculer l’itinéraire**](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) : Calculez un itinéraire et traitez aussitôt la requête. Cette API prend en charge les requêtes GET et POST. Les requêtes POST sont recommandées lors de la spécification d'un grand nombre de points de cheminement ou lors de l'utilisation de nombreuses options de routage pour s'assurer que la requête URL ne devienne pas trop longue et n’entraîne des problèmes.
- [**Itinéraire par lots**](https://docs.microsoft.com/rest/api/maps/route/postroutedirectionsbatchpreview) : Créez une requête contenant jusqu'à 1 000 requêtes d’itinéraire, puis traitez-les sur une certaine période. Toutes les données seront traitées en parallèle sur le serveur et, une fois l’opération terminée, l’ensemble complet des résultats pourra être téléchargé.
- [**Services de mobilité**](https://docs.microsoft.com/rest/api/maps/mobility) : Calculez des itinéraires et des directions en utilisant les transports en commun.

Le tableau suivant référence de manière croisée les paramètres de l’API Google Maps et les paramètres d’API comparables dans Azure Maps.

| Paramètre d’API Google Maps    | Paramètre d’API Azure Maps comparable  |
|------------------------------|--------------------------------------|
| `alternatives`                 | `maxAlternatives`                  |
| `arrival_time`                | `arriveAt`                          |
| `avoid`                        | `avoid`                            |
| `departure_time`              | `departAt`                          |
| `destination`                  | `query` – coordonnées au format `"lat0,lon0:lat1,lon1…."`  |
| `key`                          | `subscription-key` : voir également la documentation [Authentification avec Azure Maps](azure-maps-authentication.md). |
| `language`                     | `language` : voir la documentation [Langues prises en charge](supported-languages.md).   |
| `mode`                         | `travelMode`                       |
| `optimize`                     | `computeBestOrder`                 |
| `origin`                       | `query`                            |
| `region`                       | *N/A* : cette fonctionnalité est liée au géocodage. Utilisez le paramètre *countrySet* lorsque vous utilisez l'API de géocodage d’Azure Maps.  |
| `traffic_model`               | *N/A* : permet uniquement de spécifier si les données de trafic doivent être utilisées avec le paramètre *traffic*. |
| `transit_mode`                | Voir la [documentation sur les services de mobilité](https://docs.microsoft.com/rest/api/maps/mobility) |
| `transit_routing_preference` | Voir la [documentation sur les services de mobilité](https://docs.microsoft.com/rest/api/maps/mobility) |
| `units`                        | *N/A* : Azure Maps utilise uniquement le système métrique.  |
| `waypoints`                    | `query`                            |

> [!TIP]
> Par défaut, l'API de routage Azure Maps ne renvoie qu'un résumé (distance et temps) et les coordonnées de l’itinéraire. Utilisez le paramètre `instructionsType` pour obtenir des instructions de navigation étape par étape. Le paramètre `routeRepresentation` peut être utilisé pour filtrer le résumé et l’itinéraire.

L’API de routage d'Azure Maps possède de nombreuses fonctionnalités supplémentaires non disponibles dans Google Maps et qui pourraient être utiles à intégrer lors de la migration de votre application :

- Prise en charge du type d'itinéraire : le plus court, le plus rapide, vers plusieurs destinations simultanément, et le plus économique en carburant.
- Prise en charge de modes de déplacement supplémentaires : bus, moto, taxi, camion et camionnette.
- Prise en charge de 150 points de cheminement.
- Calculez plusieurs temps de parcours dans une même requête ; trafic historique, trafic en direct, aucun trafic.
- Autres types de routes à éviter : itinéraires de covoiturage, routes non goudronnées, routes déjà utilisées.
- Spécifiez des zones personnalisées à éviter.
- Limitez l’altitude maximale de l’itinéraire.
- Routage basé sur les caractéristiques du moteur. Calculez les itinéraires pour les véhicules à combustion ou électriques en fonction de la réserve de carburant ou d’électricité, et des caractéristiques du moteur.
- Prise en charge des paramètres d'itinéraire des véhicules commerciaux ; dimensions du véhicule, poids, nombre d’essieux et type de cargaison.
- Spécifiez la vitesse maximale du véhicule.

En outre, le service de routage d’Azure Maps prend également en charge [le calcul des plages d’itinéraires](https://docs.microsoft.com/rest/api/maps/route/getrouterange), également connues sous le nom d’isochrones, qui génèrent un polygone couvrant une zone pouvant être parcourue dans n’importe quelle direction à partir d’un point d’origine dans une période définie ou avec une quantité de carburant ou une charge spécifiée.

## <a name="retrieve-a-map-image"></a>Récupérer une image de carte

Azure Maps fournit une API pour le rendu des images de carte statiques avec données superposées. L'API de [rendu des images de carte](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile) d’Azure Maps est comparable à l'API de carte statique de Google Maps.

> [!NOTE]
> Azure Maps exige que le centre ainsi que tous les emplacements des marqueurs et des tracés soient des coordonnées au format « longitude, latitude », alors que Google Maps utilise le format « latitude, longitude ». Les adresses devront d’abord être géocodées.

Le tableau suivant référence de manière croisée les paramètres de l’API Google Maps et les paramètres d’API comparables dans Azure Maps.

| Paramètre d’API Google Maps | Paramètre d’API Azure Maps comparable  |
|---------------------------|--------------------------------------|
| `center`                    | `center`                           |
| `format`                    | `format` : spécifié comme faisant partie du chemin d'accès à l’URL. Actuellement, seul le format PNG est pris en charge. |
| `key`                       | `subscription-key` : voir également la documentation [Authentification avec Azure Maps](azure-maps-authentication.md). |
| `language`                  | `language` : voir la documentation [Langues prises en charge](supported-languages.md).  |
| `maptype`                   | `layer` et `style` : voir la documentation [Styles de carte pris en charge](supported-map-styles.md). |
| `markers`                   | `pins`                             |
| `path`                      | `path`                             |
| `region`                    | *N/A* : cette fonctionnalité est liée au géocodage. Utilisez le paramètre `countrySet` lorsque vous utilisez l'API de géocodage d’Azure Maps.  |
| `scale`                     | *N/A*                              |
| `size`                      | `width` et `height` : peut avoir une taille maximale de 8 192 x 8 192. |
| `style`                     | *N/A*                              |
| `visible`                   | *N/A*                              |
| `zoom`                      | `zoom`                             |

> [!NOTE]
> Azure Maps utilise un système de mosaïques dont la taille est le double de celle des mosaïques de carte utilisées dans Google Maps. Ainsi, la valeur du niveau de zoom dans Azure Maps apparaîtra un niveau de zoom plus proche dans Azure Maps par rapport à Google Maps. Pour compenser cette différence, réduisez d'une unité le niveau de zoom dans les requêtes que vous migrez.

Pour plus d’informations, consultez le [Guide pratique de l'API de rendu des images de carte](how-to-render-custom-data.md).

En plus de pouvoir générer une image de carte statique, le service de rendu Azure Maps permet également d’accéder directement aux mosaïques de la carte au format raster (PNG) et vectoriel :

- [**Mosaïque de la carte**](https://docs.microsoft.com/rest/api/maps/render/getmaptile) : Récupérez les mosaïques raster (PNG) et vectorielles pour les cartes de base (routes, limites, fond).
- [**Mosaïque d’imagerie de carte**](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile) : Récupérez les mosaïques d'images aériennes et satellites.

> [!TIP]
> De nombreuses applications Google Maps sont passées, il y a quelques années, d’une expérience cartographique interactive à des images cartographiques statiques afin de réduire les coûts. Dans Azure Maps, il est souvent beaucoup plus rentable d’utiliser la commande de carte interactive dans le SDK web car elle se charge en fonction de la charge des mosaïques de la carte. Les mosaïques de carte dans Azure Maps sont grandes, et il suffit souvent de quelques mosaïques pour recréer la même vue de carte qu’une carte statique, et les mosaïques de carte sont mises en cache automatiquement par le navigateur. Ainsi, la commande de carte interactive ne génère souvent qu’une fraction d’une transaction lors de la reproduction d’une vue de carte statique. Les fonctions de panoramique et de zoom chargeront davantage de mosaïques, mais des options de contrôle de la carte vous permettent de désactiver ce comportement, si vous le souhaitez. Le contrôle de carte interactif offre également beaucoup plus d’options de visualisation que les services de carte statique.

### <a name="marker-url-parameter-format-comparison"></a>Comparaison du format des paramètres URL des marqueurs

**Avant : Google Maps**

Dans Google Maps, des marqueurs peuvent être ajoutés à une image de carte statique en utilisant le paramètre `markers` dans l’URL. Le paramètre `markers` prend en compte un style et une liste d’emplacements à afficher sur la carte avec ce style, comme indiqué ci-dessous :

```
&markers=markerStyles|markerLocation1|markerLocation2|...
```

Des styles supplémentaires peuvent être utilisés en ajoutant des paramètres `markers` supplémentaires à l'URL avec un style et un jeu d’emplacements différents.

Les emplacements des marqueurs sont spécifiés au format « latitude, longitude ».

Les styles de marqueurs dans Google Maps sont ajoutés au format `optionName:value`, avec plusieurs styles séparés par un caractère barre verticale (\|), par exemple « optionName1:value1\|optionName2:value2 ». Notez que les noms et les valeurs des options sont séparés par deux points ( :). Les noms d’options de style suivants peuvent être utilisés pour appliquer un style à des marqueurs dans Google Maps :

- `color` : couleur de l’icône du marqueur par défaut. Peut être une couleur hexadécimale 24 bits (`0xrrggbb`) ou une des valeurs suivantes ; `black`, `brown`, `green`, `purple`, `yellow`, `blue`, `gray`, `orange`, `red`, `white`.
- `label` : caractère alphanumérique majuscule unique à afficher en haut de l’icône.
- `size` : taille du marqueur. Peut être `tiny`, `mid` ou `small`.

Des icônes personnalisées peuvent également être utilisées dans Google Maps en utilisant les noms d’options de style suivants :

- `anchor` : spécifie comment aligner l’image de l’icône à la coordonnée. Peut être une valeur de pixel (x,y) ou l’une des valeurs suivantes ; `top`, `bottom`, `left`, `right`, `center`, `topleft`, `topright`, `bottomleft` ou `bottomright`.
- `icon` : URL pointant vers l’image de l’icône.

Par exemple, dans Google Maps, un marqueur rouge de taille moyenne peut être ajouté à la carte aux coordonnées (longitude : -110, latitude : 45) avec le paramètre d’URL suivant :

```
&markers=color:red|size:mid|45,-110
```

<center>

![Marqueur Google Maps](media/migrate-google-maps-web-services/google-maps-marker.png)</center>

**Après : Azure Maps**

Dans Azure Maps, des marqueurs peuvent également être ajoutés à une image de carte statique en spécifiant le paramètre `pins` dans l’URL. Comme dans Google Maps, un style et une liste d’emplacements peuvent être spécifiés dans ce paramètre, et le paramètre `pins` peut être spécifié plusieurs fois pour prendre en charge différents styles de marqueurs.

```
&pins=iconType|pinStyles||pinLocation1|pinLocation2|...
```

Des styles supplémentaires peuvent être utilisés en ajoutant des paramètres `pins` supplémentaires à l'URL avec un style et un jeu d’emplacements différents.

Au niveau des emplacements d’épingles, Azure Maps exige que les coordonnées soient au format « longitude latitude », tandis que Google Maps utilise le format « latitude,longitude ». Notez également qu'un espace, et non une virgule, sépare la longitude et la latitude dans Azure Maps.

La valeur `iconType` spécifie le type d’épingle à créer et peut avoir les valeurs suivantes :

- `default` : icône d’épingle par défaut.
- `none` : aucune icône n’apparaît, seules les étiquettes seront affichées.
- `custom` : spécifie une icône personnalisée à utiliser. Une URL pointant vers l'image de l'icône peut être ajoutée à la fin du paramètre `pins`, après les informations d’emplacement de l’épingle.
- `{udid}` : identifiant unique de données (UDID) pour une icône stockée dans la plateforme de stockage de données Azure Maps.

Les styles d’épingles dans Azure Maps sont ajoutés au format `optionNameValue`, avec plusieurs styles séparés par un caractère barre verticale (\|), par exemple `iconType|optionName1Value1|optionName2Value2`. Notez que les noms et les valeurs des options ne sont pas séparés. Les noms d’options de style suivants peuvent être utilisés pour appliquer un style à des marqueurs dans Azure Maps :

- `al` : spécifie l’opacité (alpha) du marqueur. Peut être un nombre compris entre 0 et 1.
- `an` : spécifie l’ancrage de l’épingle. Les valeurs des pixels X et y sont spécifiées au format « x y ».
- `co` : couleur de l’épingle. Doit être une couleur hexadécimale 24 bits : `000000` à `FFFFFF`.
- `la` : spécifie l’ancrage de l’étiquette. Les valeurs des pixels X et y sont spécifiées au format « x y ».
- `lc` : couleur de l’étiquette. Doit être une couleur hexadécimale 24 bits : `000000` à `FFFFFF`.
- `ls` : taille de l’étiquette en pixels. Peut être un nombre supérieur à 0.
- `ro` : valeur en degrés de la rotation de l’icône. Peut être un nombre compris entre -360 et 360.
- `sc` : valeur de mise à l'échelle de l’icône de l’épingle. Peut être un nombre supérieur à 0.

Des valeurs d’étiquette sont spécifiées pour chaque emplacement d’épingle, au lieu d’utiliser une seule valeur d'étiquette qui s'applique à tous les marqueurs de la liste des emplacements. La valeur de l’étiquette peut être une chaîne de plusieurs caractères et être entourée de guillemets simples pour éviter de la confondre avec une valeur de style ou d'emplacement.

Par exemple, dans Azure Maps, une icône rouge (`FF0000`) par défaut avec l'étiquette « Space Needle » positionnée sous (15 50) l'icône aux coordonnées (longitude : -122.349300, latitude : 47.620180) peut être ajoutée avec le paramètre URL suivant :

```
&pins=default|coFF0000|la15 50||'Space Needle' -122.349300 47.620180
```

<center>

![Marqueur Azure Maps](media/migrate-google-maps-web-services/azure-maps-marker.png)</center>

L’exemple suivant ajoute trois épingles avec les valeurs d'étiquette '1', '2' et '3' :

```
&pins=default||'1'-122 45|'2'-119.5 43.2|'3'-121.67 47.12
```

<center>

![Marqueurs multiples Azure Maps](media/migrate-google-maps-web-services/azure-maps-multiple-markers.png)</center>

### <a name="path-url-parameter-format-comparison"></a>Comparaison du format du paramètre URL des chemins d'accès

**Avant : Google Maps**

Dans Google Maps, des lignes et des polygones peuvent être ajoutés à une image de carte statique en utilisant le paramètre `path` dans l’URL. Le paramètre `path` prend en compte un style et une liste d’emplacements à afficher sur la carte avec ce style, comme indiqué ci-dessous :

```
&path=pathStyles|pathLocation1|pathLocation2|...
```

Des styles supplémentaires peuvent être utilisés en ajoutant des paramètres `path` supplémentaires à l'URL avec un style et un jeu d’emplacements différents.

Les emplacements de chemins d’accès dans Google Maps sont spécifiés avec le format `latitude1,longitude1|latitude2,longitude2|…`. Les chemins d’accès peuvent être encodés ou contenir des adresses de points.

Les styles de chemins d'accès dans Google Maps sont ajoutés au format `optionName:value`, avec plusieurs styles séparés par un caractère barre verticale (\|), par exemple `optionName1:value1|optionName2:value2`. Notez que les noms et les valeurs des options sont séparés par deux points ( :). Les noms d’options de style suivants peuvent être utilisés pour appliquer un style à des chemins d'accès dans Google Maps :

- `color` : couleur du contour du chemin d'accès ou du polygone. Peut être une couleur hexadécimale 24 bits (`0xrrggbb`), une couleur hexadécimale 32 bits (`0xrrggbbbaa`) ou l'une des valeurs suivantes : noir, brun, vert, violet, jaune, bleu, gris, orange, rouge, blanc.
- `fillColor` : couleur de remplissage de la zone du chemin d'accès avec (polygone). Peut être une couleur hexadécimale 24 bits (`0xrrggbb`), une couleur hexadécimale 32 bits (`0xrrggbbbaa`) ou l'une des valeurs suivantes : noir, brun, vert, violet, jaune, bleu, gris, orange, rouge, blanc.
- `geodesic` : indique si le chemin d’accès doit être une ligne qui suit la courbure de la Terre.
- `weight` : épaisseur de la ligne du chemin d'accès en pixels.

Par exemple, dans Google Maps, une ligne rouge avec une opacité de 50 % et une épaisseur de 4 pixels peut être ajoutée à la carte entre les coordonnées (longitude : -110, latitude : 45 et longitude : -100, latitude : 50) avec le paramètre d’URL suivant :

```
&path=color:0xFF000088|weight:4|45,-110|50,-100
```

<center>

![Polyligne Google Maps](media/migrate-google-maps-web-services/google-maps-polyline.png)</center>

**Après : Azure Maps**

Dans Azure Maps, des lignes et des polygones peuvent également être ajoutés à une image de carte statique en spécifiant le paramètre `path` dans l’URL. Comme dans Google Maps, un style et une liste d’emplacements peuvent être spécifiés dans ce paramètre, et le paramètre `path` peut être spécifié plusieurs fois pour afficher plusieurs cercles, lignes et polygones de différents styles.

```
&path=pathStyles||pathLocation1|pathLocation2|...
```

Au niveau des emplacements de chemins d'accès, Azure Maps exige que les coordonnées soient au format « longitude latitude », tandis que Google Maps utilise le format « latitude,longitude ». Notez également qu'un espace, et non une virgule, sépare la longitude et la latitude dans Azure Maps. Azure Maps ne prend pas les chemins ou les adresses encodés pour des points. De plus grands jeux de données peuvent être téléchargés lorsqu'un GeoJSON remplit l'API de stockage de données Azure Maps, comme documenté [ici](how-to-render-custom-data.md#get-data-from-azure-maps-data-storage).

Les styles de chemins d'accès dans Azure Maps sont ajoutés au format `optionNameValue`, avec plusieurs styles séparés par un caractère barre verticale (\|), par exemple `optionName1Value1|optionName2Value2`. Notez que les noms et les valeurs des options ne sont pas séparés. Les noms d’options de style suivants peuvent être utilisés pour appliquer un style à des chemins d'accès dans Azure Maps :

- `fa` : opacité de la couleur de remplissage (alpha) utilisée lors du rendu des polygones. Peut être un nombre compris entre 0 et 1.
- `fc` : couleur de remplissage utilisée pour rendre l’aire d'un polygone.
- `la` : opacité de la couleur des lignes (alpha) utilisée lors du rendu des lignes et du contour des polygones. Peut être un nombre compris entre 0 et 1.
- `lc` : couleur de ligne utilisée pour rendre les lignes et le contour des polygones.
- `lw` : largeur de la ligne en pixels.
- `ra` : spécifie un rayon de cercle en mètres.

Par exemple, dans Azure Maps, une ligne rouge avec une opacité de 50 % et une épaisseur de 4 pixels peut être ajoutée à la carte entre les coordonnées (longitude : -110, latitude : 45 et longitude : -100, latitude : 50) avec le paramètre d’URL suivant :

```
&path=lcFF0000|la.5|lw4||-110 45|-100 50
```

<center>

![Polyligne Azure Maps](media/migrate-google-maps-web-services/azure-maps-polyline.png)</center>

## <a name="calculate-a-distance-matrix"></a>Calculer une matrice des distances

Azure Maps fournit une API pour le calcul des durées de déplacement et des distances entre un jeu d'emplacements sous forme de matrice des distances. L'API de la matrice des distances Azure Maps est comparable à l'API de la matrice des distances de Google Maps ;

- [**Matrice d’itinéraire**](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview) : Calcule de manière asynchrone les durées de déplacement et les distances pour un jeu d'origines et de destinations. Jusqu'à 700 cellules par requête sont prises en charge (le nombre d'origines multiplié par le nombre de destinations). En gardant cette contrainte à l'esprit, voici des exemples de dimensions matricielles possibles : 700x1, 50x10, 10x10, 28x25, 10x70.

> [!NOTE]
> Une requête à l'API de matrice des distances ne peut être faite qu'en utilisant une requête POST avec les informations d'origine et de destination dans le corps de la requête. De plus, Azure Maps exige que toutes les origines et destinations soient des coordonnées. Les adresses devront d’abord être géocodées.

Le tableau suivant référence de manière croisée les paramètres de l’API Google Maps et les paramètres d’API comparables dans Azure Maps.

| Paramètre d’API Google Maps      | Paramètre d’API Azure Maps comparable  |
|--------------------------------|--------------------------------------|
| `arrivial_time`                | `arriveAt`                           |
| `avoid`                        | `avoid`                              |
| `depature_time`                | `departAt`                           |
| `destinations`                 | `destination` : spécifiez GeoJSON dans le corps de la requête POST. |
| `key`                          | `subscription-key` : voir également la documentation [Authentification avec Azure Maps](azure-maps-authentication.md). |
| `language`                     | `language` : voir la documentation [Langues prises en charge](supported-languages.md).  |
| `mode`                         | `travelMode`                         |
| `origins`                      | `origins` : spécifiez GeoJSON dans le corps de la requête POST.  |
| `region`                       | *N/A* : cette fonctionnalité est liée au géocodage. Utilisez le paramètre `countrySet` lorsque vous utilisez l'API de géocodage d’Azure Maps. |
| `traffic_model`                | *N/A* : permet uniquement de spécifier si les données de trafic doivent être utilisées avec le paramètre `traffic`. |
| `transit_mode`                 | *N/A* : les matrices de distance basées sur le transit ne sont actuellement pas prises en charge.  |
| `transit_routing_preference`   | *N/A* : les matrices de distance basées sur le transit ne sont actuellement pas prises en charge.  |
| `units`                        | *N/A* : Azure Maps utilise uniquement le système métrique. |

> [!TIP]
> Toutes les options de routage avancées disponibles dans l'API de routage Azure Maps (routage de camions, caractéristiques des moteurs, éléments à éviter...) sont prises en charge dans l'API de matrice des distances Azure Maps.

## <a name="get-a-time-zone"></a>Obtenir un fuseau horaire

Azure Maps fournit une API pour obtenir le fuseau horaire dans lequel se trouve une coordonnée. L'API de fuseau horaire d’Azure Maps est comparable à l'API de fuseau horaire de Google Maps :

- [**Fuseau horaire par coordonnée**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebycoordinates) : Spécifiez une coordonnée et obtenez les détails du fuseau horaire dans lequel elle se trouve.

Le tableau suivant référence de manière croisée les paramètres de l’API Google Maps et les paramètres d’API comparables dans Azure Maps.

| Paramètre d’API Google Maps | Paramètre d’API Azure Maps comparable   |
|---------------------------|---------------------------------------|
| `key`                       | `subscription-key` : voir également la documentation [Authentification avec Azure Maps](azure-maps-authentication.md).       |
| `language`                  | `language` : voir la documentation [Langues prises en charge](supported-languages.md).    |
| `location`                  | `query`             |
| `timestamp`                 | `timeStamp`         |

Par ailleurs, la plateforme Azure Maps fournit également un certain nombre d'API de fuseau horaire supplémentaires pour faciliter les conversions avec les noms de fuseaux horaires et les ID :

- [**Fuseau horaire par ID**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebyid) : Renvoie les informations de fuseau horaire actuelles, historiques et futures pour l'ID de fuseau horaire IANA spécifié.
- [**Fuseau horaire avec Enum IANA**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumiana) : Renvoie une liste complète des ID de fuseau horaire IANA. Les mises à jour du service IANA sont reflétées dans le système dans un délai de 24 heures.
- [**Fuseau horaire avec Enum Windows**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumwindows) : Renvoie une liste complète des ID de fuseau horaire Windows.
- [**Fuseau horaire avec version IANA**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneianaversion) : Renvoie le numéro de la version actuelle IANA utilisée par Azure Maps.
- [**Fuseau horaire Windows à IANA**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonewindowstoiana) : Renvoie un ID IANA correspondant, avec un ID de fuseau horaire Windows valide. Plusieurs ID IANA peuvent être renvoyés pour un seul ID Windows.

## <a name="client-libraries"></a>Bibliothèques clientes

Azure Maps fournit des bibliothèques clientes pour les langages de programmation suivants :

- JavaScript, TypeScript, Node.js : [documentation](how-to-use-services-module.md) \| [Package NPM](https://www.npmjs.com/package/azure-maps-rest)

Bibliothèques clientes open source pour d'autres langages de programmation :

- .NET Standard 2.0 : [projet GitHub](https://github.com/perfahlen/AzureMapsRestServices) \| [package NuGet](https://www.nuget.org/packages/AzureMapsRestToolkit/)

## <a name="additional-resources"></a>Ressources supplémentaires

Voici quelques documents et ressources supplémentaires sur les services REST Azure Maps.

- [Bonnes pratiques pour la recherche](how-to-use-best-practices-for-search.md)
- [Rechercher une adresse](how-to-search-for-address.md)
- [Documentation de référence sur l’API de service REST Azure Maps](https://docs.microsoft.com/rest/api/maps/)

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les services REST Azure Maps.

> [!div class="nextstepaction"]
> [Meilleures pratiques pour l'utilisation du service de recherche](how-to-use-best-practices-for-search.md)

> [!div class="nextstepaction"]
> [Comment utiliser le module de services (SDK web)](how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Exemples de code](https://docs.microsoft.com/samples/browse/?products=azure-maps)