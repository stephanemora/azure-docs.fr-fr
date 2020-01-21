---
title: Vue d’ensemble | Microsoft Azure Maps
description: Dans cet article, vous allez découvrir les services et les fonctionnalités de Microsoft Azure Maps, et apprendre à les utiliser dans vos applications.
author: walsehgal
ms.author: v-musehg
ms.date: 02/04/2019
ms.topic: overview
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 353850cacb06dcc8a0db1b957114db314d7002a3
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911846"
---
# <a name="what-is-azure-maps"></a>Qu’est-ce qu’Azure Maps ?

Azure Maps est une collection de services géospatiaux qui utilisent des données cartographiques pour fournir un contexte géographique précis à des applications web et mobiles. Azure Maps fournit les services suivants :

* API REST pour le rendu des cartes dans plusieurs styles et dans une imagerie satellitaire.
* Recherches d’adresses, de lieux et de points d’intérêt dans le monde entier.
* Itinéraires point à point, multipoints, optimisation multipoint, isochrone, véhicule commercial, trafic influencé et itinéraires par matrice ; affichage des flux et des incidents de trafic.
* Services de mobilité pour les demandes de transports publics et de modes de transport alternatifs (par exemple le partage de vélo, le partage de scooter et le partage de voiture) et la planification des itinéraires en temps réel. 
* Établissement de l’emplacement de l’utilisateur via la géolocalisation et conversion de l’emplacement en fuseaux horaires. 
* Services de geofencing et de stockage de données cartographiques, avec les informations d’emplacement hébergées dans Azure. 
* Intelligence géographique via l’analytique géospatiale. 

Outre les API REST, les services Azure Maps sont disponibles via le SDK Web ou Android SDK. Ces outils aident les développeurs à développer et à mettre à l’échelle rapidement des solutions intégrant des informations de localisation dans des solutions Azure. 

Vous pouvez vous inscrire pour un [compte Azure Maps](https://azure.microsoft.com/services/azure-maps/) gratuit et commencer à développer.

La vidéo suivante explique Azure Maps plus en détail :

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Maps/player?format=ny" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="map-controls"></a>Contrôles de carte

### <a name="web-sdk"></a>Kit de développement logiciel (SDK) web

Le Kit de développement logiciel (SDK) web Azure Maps vous permet de personnaliser des cartes interactives avec du contenu et des images qui vous sont propres pour les afficher dans vos applications web ou mobiles. Ce contrôle utilise WebGL : vous pouvez donc afficher de grands jeux de données avec des performances élevées. Développez avec le SDK en utilisant JavaScript ou TypeScript.

![Exemple de carte des changements de population](media/about-azure-maps/Introduction_WebMapControl.png)

### <a name="android-sdk"></a>Kit de développement logiciel Android

Utilisez le SDK Android d’Azure Maps pour créer des applications cartographiques mobiles. 

![Exemples de cartes sur un appareil mobile](media/about-azure-maps/AndroidSDK.png)

## <a name="services-in-azure-maps"></a>Services d’Azure Maps

Azure Maps se compose des neuf services suivants, qui peuvent fournir un contexte géographique à vos applications Azure.

### <a name="data-service"></a>Data Service

Les données sont un impératif pour les cartes. Utilisez Data Service pour charger et stocker des données géospatiales pour une utilisation avec des opérations spatiales ou la composition d’images.  Le fait de pouvoir rapprocher les données client du service Azure Maps permettra de réduire la latence, d’améliorer la productivité et de créer de nouveaux scénarios dans vos applications. Pour plus d’informations sur ce service, consultez la [Documentation de l’API Data Service](https://docs.microsoft.com/rest/api/maps/data).

### <a name="mobility-service"></a>Service Mobilité

Azure Maps Mobility Service permet la planification de déplacements en temps réel. Il retourne les options des meilleurs itinéraires possibles et fournit différents modes de déplacement. Pour les zones urbaines (villes), ces modes peuvent inclure la marche, le vélo et les transports publics. Les développeurs peuvent demander des détails sur les itinéraires des transports, comme le plan des lignes, les listes des arrêts, planifiées les arrivées planifiées et en temps réel, et les alertes sur les services.

Le service permet également les recherches de types d’objets spécifiques, comme les vélos, les scooters ou les voitures partagés autour d’un emplacement. Les utilisateurs peuvent demander combien de vélos partagés disponibles se trouvent dans la station la plus proche. Ils peuvent rechercher des véhicules partagés disponibles, et trouver des détails comme la disponibilité à venir et le niveau de carburant actuel.

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

Le service Spatial Operations d’Azure Maps prend les informations d’emplacement et les analyse à la volée afin d’informer les clients des événements en cours qui se produisent dans le temps et l’espace. Il permet une analyse en quasi temps réel et la modélisation prédictive des événements. 

Le service permet aux clients d’améliorer leur intelligence de localisation avec une bibliothèque de calculs mathématiques géospatiaux courants, notamment le point le plus proche, la distance orthodromique et les zones tampons. Pour plus d’informations sur le service et ses différentes fonctionnalités, consultez la [documentation de l’API Spatial Operations](https://docs.microsoft.com/rest/api/maps/spatial).

### <a name="time-zone-service"></a>Time Zone Service

Time Zone Service permet d’interroger des informations actuelles, historiques et futures sur un fuseau horaire à l’aide de paires latitude-longitude ou d’un [ID IANA](https://www.iana.org/). Time Zone Service permet également de :

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

Le service IP to Location vous permet de prévisualiser le code pays à deux lettres récupéré d’une adresse IP. Ce service peut vous aider à adapter et à améliorer l’expérience utilisateur en personnalisant le contenu des applications en fonction de l’emplacement géographique.

Pour plus d’informations sur les API REST pour le service IP to Location, consultez la [Documentation de l’API de géolocalisation d’Azure Maps](https://docs.microsoft.com/rest/api/maps/geolocation).

## <a name="programming-model"></a>Modèle de programmation

Azure Maps est conçu pour la mobilité et peut vous permettre de développer des applications multiplateformes. Il utilise un modèle de programmation indépendant du langage et il prend en charge les sorties au format JSON via des [API REST](https://docs.microsoft.com/rest/api/maps/).

En outre, Azure Maps fournit un [contrôle de carte JavaScript](https://docs.microsoft.com/javascript/api/azure-maps-control) pratique avec un modèle de programmation simple, ce qui facilite et accélère le développement des applications web et mobiles.

## <a name="usage"></a>Usage

Vous pouvez accéder aux services Azure Maps via le [portail Azure](https://portal.azure.com), en créant un compte Azure Maps.

Azure Maps utilise un schéma d’authentification basé sur une clé. Votre compte est accompagné de deux clés déjà générées pour vous. Commencez par intégrer ces fonctionnalités de localisation dans votre application en utilisant l’une des clés et en formulant une requête au service Azure Maps.

## <a name="supported-regions"></a>Régions prises en charge

Les API d’Azure Maps sont actuellement disponibles dans tous les pays/régions, à l’exception des suivants :

* Chine
* Corée du Sud

Vérifiez que l’emplacement de votre adresse IP actuelle ne se trouve pas dans un pays non pris en charge dans la liste ci-dessus.

## <a name="next-steps"></a>Étapes suivantes

Essayez un exemple d’application qui montre Azure Maps :

> [!div class="nextstepaction"]
> [Démarrage rapide : Créer une application web](quick-demo-map-app.md)

Restez informé sur Azure Maps : 

> [!div class="nextstepaction"]
> [Blog Azure Maps](https://azure.microsoft.com/blog/topics/azure-maps/)
