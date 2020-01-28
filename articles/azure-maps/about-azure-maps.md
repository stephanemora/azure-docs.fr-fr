---
title: Vue d’ensemble | Microsoft Azure Maps
description: Découvrez plus d’informations sur les services et les fonctionnalités de Microsoft Azure Maps, et comment les utiliser dans vos applications.
author: walsehgal
ms.author: v-musehg
ms.date: 01/15/2020
ms.topic: overview
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 2909dbebc7531be0e45b321cc31b599b0f63ee4a
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76262569"
---
# <a name="what-is-azure-maps"></a>Qu’est-ce qu’Azure Maps ?

Azure Maps est une collection de services géospatiaux qui utilisent des données cartographiques actualisées pour fournir un contexte géographique précis à des applications web et mobiles. Azure Maps fournit les services suivants :

* API REST pour le rendu des cartes dans plusieurs styles et dans une imagerie satellitaire.
* Recherches d’adresses, de lieux et de points d’intérêt dans le monde entier.
* Différents types d’itinéraires : point à point, multipoint, optimisation multipoint, isochrone, véhicule commercial, trafic influencé et itinéraire par matrice
* Vue des flux de trafic et des incidents.
* Services de mobilité pour les demandes de transports publics et de modes de transport alternatifs (par exemple le partage de vélo, le partage de scooter et le partage de voiture) et la planification des itinéraires en temps réel. 
* Établissement de l’emplacement de l’utilisateur via la géolocalisation et conversion de l’emplacement en fuseaux horaires. 
* Services de geofencing et de stockage de données cartographiques, avec les informations d’emplacement hébergées dans Azure. 
* Intelligence géographique via l’analytique géospatiale. 

Par ailleurs, les services Azure Maps sont disponibles via le kit SDK Web ou Android SDK. Ces outils aident les développeurs à développer et à mettre à l’échelle rapidement des solutions intégrant des informations de localisation dans des solutions Azure. 

Vous pouvez vous inscrire pour un [compte Azure Maps](https://azure.microsoft.com/services/azure-maps/) gratuit et commencer à développer.

La vidéo suivante explique Azure Maps plus en détail :

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Maps/player?format=ny" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="map-controls"></a>Contrôles de carte

### <a name="web-sdk"></a>Kit de développement logiciel (SDK) web

Le SDK Web Azure Maps vous permet de personnaliser des cartes interactives avec du contenu et des images qui vous sont propres pour vos applications web ou mobiles. Ce contrôle utilise WebGL : vous pouvez donc afficher de grands jeux de données avec des performances élevées. Développez avec le SDK en utilisant JavaScript ou TypeScript.

![Exemple de carte des changements de population](media/about-azure-maps/Introduction_WebMapControl.png)

### <a name="android-sdk"></a>Kit de développement logiciel Android

Utilisez le SDK Android d’Azure Maps pour créer des applications cartographiques mobiles. 

![Exemples de cartes sur un appareil mobile](media/about-azure-maps/AndroidSDK.png)

## <a name="services-in-azure-maps"></a>Services d’Azure Maps

Azure Maps se compose des neuf services suivants, qui peuvent fournir un contexte géographique à vos applications Azure.

### <a name="data-service"></a>Data Service

Les données sont indispensables pour les cartes. Utilisez Data Service pour charger et stocker des données géospatiales pour une utilisation avec des opérations spatiales ou la composition d’images.  Le fait de pouvoir rapprocher les données client du service Azure Maps permettra de réduire la latence, d’améliorer la productivité et de créer de nouveaux scénarios dans vos applications. Pour plus d’informations sur ce service, consultez la [Documentation de l’API Data Service](https://docs.microsoft.com/rest/api/maps/data).

### <a name="mobility-service"></a>Service Mobilité

Azure Maps Mobility Service permet la planification de déplacements en temps réel. Il retourne les options des meilleurs itinéraires possibles et fournit différents modes de déplacement. Pour les zones urbaines (villes), ces modes peuvent inclure la marche, le vélo et les transports publics. Vous pouvez demander des itinéraires des transports, le plan des lignes, les listes des arrêts, les arrivées planifiées et en temps réel, et des alertes sur les services.

Le service permet également les recherches de types d’objets spécifiques, comme les vélos, les scooters ou les voitures partagés autour d’un emplacement. Les utilisateurs peuvent demander le nombre de vélos disponibles dans la station la plus proche et rechercher les véhicules disponibles pour le covoiturage. Ils peuvent aussi trouver des détails comme la disponibilité future des véhicules et le niveau de carburant actuel.

Pour plus d’informations sur le service, consultez la [Documentation de l’API Mobility](https://docs.microsoft.com/rest/api/maps/mobility).

### <a name="render-service"></a>Render Service

Render Service permet aux développeurs de créer des applications web et mobiles autour de la cartographie. Le service utilise des images graphiques raster de haute qualité disponibles en 19 niveaux de zoom ou des images de cartes au format vectoriel entièrement personnalisables.

![Exemple de carte provenant de Render Service](media/about-azure-maps/Introduction_Map.png)

Render Service inclut désormais des API pour permettre aux développeurs de travailler avec des images satellite. Pour plus d’informations, consultez la [Documentation de l’API Render](https://docs.microsoft.com/rest/api/maps/render).

### <a name="route-service"></a>Service d’itinéraire

Route Service comporte des fonctionnalités robustes de calcul géométrique pour les infrastructures réelles et des directions pour différents modes de transport. Le service permet aux développeurs de calculer des directions pour plusieurs modes de déplacement, notamment en voiture, en camion, à vélo ou à pied. Le service peut également prendre en compte d’autres entrées, par exemple, les conditions de circulation, les restrictions de poids et le transport de matières dangereuses.

![Exemple de carte provenant de Route Service](media/about-azure-maps/Introduction_Route.png)

Route Service offre un aperçu de fonctionnalités avancées comme les suivantes : 

* Traitement par lots de plusieurs demandes d’itinéraire.
* Matrices de durées et de distances des trajets entre un ensemble d’origines et de destinations.
* Recherche des itinéraires ou des distances que les utilisateurs peuvent parcourir en fonction d’exigences horaires ou de carburant. 

Pour plus d’informations sur les fonctionnalités liées aux itinéraires, consultez la [Documentation de l’API Route](https://docs.microsoft.com/rest/api/maps/route).

### <a name="search-service"></a>Service de recherche

Search Service aide les développeurs à rechercher des adresses, des lieux, des listes d’entreprises par nom ou par catégorie, et d’autres informations d’ordre géographique. Search Service peut aussi [géocoder en inversé](https://en.wikipedia.org/wiki/Reverse_geocoding) les adresses et rues de croisement en fonction des latitudes et longitudes.

![Exemple d’une recherche sur une carte](media/about-azure-maps/Introduction_Search.png)

Search Service fournit également des fonctionnalités avancées comme les suivantes :

* Rechercher le long d’une route.
* Rechercher à l’intérieur d’une zone plus large.
* Traiter par lot un groupe de demandes de recherche.
* Rechercher dans une zone plus large au lieu d’un emplacement précis. 

Les API pour le traitement par lot et la recherche par zone sont actuellement en préversion. Pour plus d’informations sur les fonctionnalités de recherche, consultez la [Documentation de l’API Search](https://docs.microsoft.com/rest/api/maps/search).

### <a name="spatial-operations-service"></a>Service Spatial Operations

Le service Spatial Operations d’Azure Maps prend les informations de localisation et les analyse rapidement afin d’informer les clients des événements en cours qui se produisent dans le temps et l’espace. Il permet une analyse en quasi temps réel et la modélisation prédictive des événements. 

Le service permet aux clients d’améliorer leurs informations de localisation, avec une bibliothèque de calculs mathématiques géospatiaux courants. Les calculs courants incluent le point le plus proche, la distance orthodromique et les zones tampons. Pour plus d’informations sur le service et ses différentes fonctionnalités, consultez la [documentation de l’API Spatial Operations](https://docs.microsoft.com/rest/api/maps/spatial).

### <a name="time-zone-service"></a>Time Zone Service

Time Zone Service permet d’interroger des informations actuelles, historiques et futures sur les fuseaux horaires. Vous pouvez utiliser des combinaisons de latitude et de longitude, ou un [ID IANA](https://www.iana.org/). Time Zone Service permet également de :

* Convertir les ID de fuseau horaire Microsoft Windows en fuseaux horaires IANA.
* Récupérer un décalage de fuseau horaire au format UTC.
* Obtenir l’heure actuelle dans un fuseau horaire. 

Voici un exemple de réponse JSON courante à une requête Time Zone Service :

```JSON
{
    "Version": "2017c",
    "ReferenceUtcTimestamp": "2017-11-20T23:09:48.686173Z",
    "TimeZones": [{
        "Id": "America/Los_Angeles",
        "ReferenceTime": {
            "Tag": "PST",
            "StandardOffset": "-08:00:00",
            "DaylightSavings": "00:00:00",
            "WallTime": "2017-11-20T15:09:48.686173-08:00",
            "PosixTzValidYear": 2017,
            "PosixTz": "PST+8PDT,M3.2.0,M11.1.0"
        }
    }]
}
```

Pour plus d’informations sur ce service, consultez la [Documentation de l’API Time Zone](https://docs.microsoft.com/rest/api/maps/timezone).

### <a name="traffic-service"></a>Traffic Service

Traffic Service est une suite de services web conçue que les développeurs peuvent utiliser pour créer des applications web et mobiles nécessitant des informations sur le trafic. Ce service fournit deux types de données :

* Flux de trafic : Vitesses et durées de déplacement observées en temps réel pour toutes les routes clés du réseau.
* Incidents de trafic : Vue actualisée des embouteillages et des incidents sur le réseau routier.

![Exemple de carte avec des informations sur le trafic](media/about-azure-maps/Introduction_Traffic.png)

Pour plus d’informations, consultez la [Documentation de l’API Traffic](https://docs.microsoft.com/rest/api/maps/traffic).

### <a name="ip-to-location-service"></a>Service IP to Location

Le service IP to Location vous permet de prévisualiser le code pays à deux lettres récupéré d’une adresse IP. Ce service peut vous aider à améliorer l’expérience utilisateur en personnalisant le contenu des applications en fonction de la localisation géographique.

Pour plus d’informations sur le service IP to Location dans l’API REST, consultez la [Documentation de l’API de géolocalisation d’Azure Maps](https://docs.microsoft.com/rest/api/maps/geolocation).

## <a name="programming-model"></a>Modèle de programmation

Azure Maps est conçu pour la mobilité et peut vous permettre de développer des applications multiplateformes. Il utilise un modèle de programmation indépendant du langage et il prend en charge les sorties au format JSON via des [API REST](https://docs.microsoft.com/rest/api/maps/).

De plus, Azure Maps offre un [contrôle de carte JavaScript](https://docs.microsoft.com/javascript/api/azure-maps-control) pratique avec un modèle de programmation simple. Le développement est simple et rapide pour les applications web et mobiles.

## <a name="usage"></a>Usage

Vous pouvez accéder aux services Azure Maps via le [portail Azure](https://portal.azure.com), en créant un compte Azure Maps.

Azure Maps utilise un schéma d’authentification basé sur une clé. Votre compte est accompagné de deux clés déjà générées pour vous. Commencez à intégrer ces fonctionnalités de localisation dans votre application en utilisant une des clés et en faisant une demande auprès du service Azure Maps.

## <a name="supported-regions"></a>Régions prises en charge

Les API Azure Maps sont actuellement disponibles dans tous les pays et régions, à l’exception de :

* Chine
* Corée du Sud

Vérifiez que la localisation de votre adresse IP actuelle se trouve dans un pays pris en charge.

## <a name="next-steps"></a>Étapes suivantes

Essayez un exemple d’application qui montre Azure Maps :

> [!div class="nextstepaction"]
> [Démarrage rapide : Créer une application web](quick-demo-map-app.md)

Restez informé sur Azure Maps : 

> [!div class="nextstepaction"]
> [Blog Azure Maps](https://azure.microsoft.com/blog/topics/azure-maps/)
