---
title: Vue d’ensemble d’Azure Maps | Microsoft Docs
description: Présentation d’Azure Maps
author: dsk-2015
ms.author: dkshir
ms.date: 07/12/2018
ms.topic: overview
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: d0ec146d276f72abf4cbbe3da4d11c0d5539df6d
ms.sourcegitcommit: 727a0d5b3301fe20f20b7de698e5225633191b06
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/19/2018
ms.locfileid: "39144191"
---
# <a name="what-is-azure-maps"></a>Qu’est-ce qu’Azure Maps ?
Azure Maps est une collection de services géospatiaux qui s’appuient sur des données de mappage actualisées pour vous permettre de donner un contexte géographique précis à vos applications web et mobiles. Elle contient des API REST pour le rendu des cartes, la recherche de points d’intérêt, la génération d’itinéraires vers les points d’intérêt, les conditions de trafic, les fuseaux horaires et les IP pour les services de localisation. Vous pouvez utiliser ces API à l’aide de vos outils habituels pour développer et mettre à l’échelle rapidement des solutions Azure intégrant des informations de localisation. En plus des API REST, elle s’accompagne d’un contrôle JavaScript web, qui garantit un développement simplifié, flexible et portable sur plusieurs supports. 

La vidéo suivante explique Azure Maps plus en détail :

<iframe src="https://channel9.msdn.com/Shows/Azure-Friday/Azure-Location-Based-Services/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="services-in-azure-maps"></a>Services d’Azure Maps

Azure Maps se compose des six services suivants, qui peuvent fournir un contexte géographique à vos applications Azure. 

### <a name="render-service"></a>Render Service

Render Service permet aux développeurs de créer des applications web et mobiles autour de la cartographie. Le service utilise des images graphiques raster de haute qualité disponibles en 19 niveaux de zoom ou des images de cartes au format vectoriel entièrement personnalisables.

![Azure Maps Map.png](media/about-azure-maps/Introduction_Map.png)

Render Service inclut désormais des API pour permettre aux développeurs de travailler avec des images satellite. Pour plus d’informations, consultez la page [Azure Maps Render APIs](https://docs.microsoft.com/rest/api/maps/render) (API Azure Maps Render).


### <a name="route-service"></a>Service d’itinéraire 

Route Service comporte des fonctionnalités robustes de calcul géométrique pour les infrastructures réelles et des directions pour différents modes de transport. Le service permet aux développeurs de calculer des directions pour plusieurs modes de déplacement, notamment en voiture, en camion, à vélo ou à pied. Le service peut également prendre en compte d’autres entrées, par exemple, les conditions de circulation, les restrictions de poids et le transport de matières dangereuses.

![Azure Maps Route.png](media/about-azure-maps/Introduction_Route.png)

Route Service offre désormais un aperçu des fonctionnalités avancées telles que le traitement par lots de plusieurs requêtes d’itinéraire, les matrices de durée de trajet et de distance entre un ensemble de points d’origines et de destinations, et la recherche des itinéraires ou distances que vous pouvez parcourir en fonction de votre temps ou de vos besoins en carburant. Pour plus d’informations sur les fonctionnalités d’itinéraire, consultez la page [Azure Maps Route APIs](https://docs.microsoft.com/rest/api/maps/route) (API Azure Maps Route).


### <a name="search-service"></a>Service de recherche

Search Service permet aux développeurs de rechercher des adresses, des lieux, des listes d’entreprises par nom ou catégorie, et d’autres informations d’ordre géographique. Search Service peut aussi [géocoder en inversé](https://en.wikipedia.org/wiki/Reverse_geocoding) les adresses et rues de croisement en fonction des latitudes et longitudes. 

![Azure Maps Search.png](media/about-azure-maps/Introduction_Search.png)

Search Service fournit également des fonctionnalités avancées telles que la recherche sur un itinéraire, la recherche à l’intérieur d’une zone plus vaste, le traitement des groupes de requêtes de recherche, ainsi que de la recherche pour une zone plus grande et pas uniquement pour un emplacement. Les API pour le traitement par lot et la recherche par zone sont actuellement en préversion. Pour plus d’informations sur les fonctionnalités de recherche, consultez la page [Azure Maps Search APIs](https://docs.microsoft.com/rest/api/maps/search) (API Azure Maps Search).


### <a name="time-zone-service"></a>Time Zone Service

Time Zone Service permet d’interroger des informations actuelles, historiques et futures sur un fuseau horaire à l’aide de paires latitude-longitude ou d’un [ID IANA](http://www.iana.org/). Time Zone Service permet aussi de convertir les ID de fuseau horaire Microsoft Windows en fuseaux horaires IANA, de récupérer un décalage de fuseau horaire par rapport à UTC et d’obtenir l’heure actuelle dans un fuseau horaire respectif. Voici un exemple de réponse JSON type à une requête Time Zone Service :

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

Pour plus d’informations sur ce service, consultez la page [Azure Maps Timezone APIs](https://docs.microsoft.com/rest/api/maps/timezone) (API Azure Maps Timezone).

### <a name="traffic-service"></a>Traffic Service

Traffic Service est une suite de services web permettant aux développeurs de créer des applications web et mobiles pour lesquelles des informations sur le trafic sont nécessaires. Ce service fournit deux types de données :
    * flux de trafic : vitesses et durées de déplacement observées en temps réel pour toutes les routes clés du réseau ; 
    * incidents de trafic : image précise des embouteillages et des incidents sur le réseau routier.

![Trafic Azure Maps](media/about-azure-maps/Introduction_Traffic.png)

Consultez la page [Azure Maps Traffic APIs](https://docs.microsoft.com/rest/api/maps/traffic) (API Azure Maps Traffic) pour plus d’informations.

### <a name="ip-to-location"></a>Localisation d’IP

Localisation d’IP est un service en préversion qui vous permet de récupérer le code de deux lettres correspondant au pays dans une adresse IP donnée. Ce service peut vous aider à adapter votre application aux contraintes géopolitiques spéciales, ainsi qu’à améliorer l’expérience utilisateur en modifiant le contenu de l’application selon leur emplacement géographique. 

Pour plus d’informations sur les API REST pour l’adresse IP du service d’emplacement, visitez la page [Azure Maps Geolocation APIs](https://docs.microsoft.com/rest/api/maps/geolocation) (API de géolocalisation Azure Maps).

## <a name="programming-model"></a>Modèle de programmation

Azure Maps est conçu pour la mobilité et peut alimenter des applications multiplateformes. Il utilise un modèle de programmation indépendant du langage et il prend en charge la sortie au format JSON via les [API REST](https://docs.microsoft.com/rest/api/maps/). 

En outre, Azure Maps fournit un [contrôle de carte JavaScript](https://docs.microsoft.com/javascript/api/azure-maps-javascript/?view=azure-iot-typescript-latest) pratique avec un modèle de programmation simple, ce qui facilite et accélère le développement des applications web et mobiles. 


## <a name="usage"></a>Usage

Vous pouvez accéder aux services Maps via le [Portail Azure](http://portal.azure.com) et en créant un compte Azure Maps. 

Azure Maps utilise un schéma d’authentification basé sur une clé. Votre compte est doté de deux clés prégénérées pour vous. Vous pouvez intégrer directement ces fonctionnalités de localisation dans vos applications en utilisant l’une de vos clés dans les requêtes envoyées au service Azure Maps.

## <a name="supported-regions"></a>Régions prises en charge
L’API Azure Maps est actuellement disponible partout à l’exception des pays suivants : 

* Argentine
* Chine
* Inde
* Maroc
* Pakistan
* Corée du Sud

Contrôlez votre adresse IP actuelle et vérifiez que son emplacement ne se trouve pas dans un pays non pris en charge de la liste ci-dessus.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les nouvelles fonctionnalités d’Azure Maps : 
    - [Route Matrix, Isochrones, IP lookup, and more](https://azure.microsoft.com/blog/route-matrix-isochrones-ip-lookup-and-more-added-to-azure-maps/) (Routage de matrices, isochrones, recherche d’IP, etc.). 
- Testez un exemple d’application qui présente le service
    - [Lancer une carte de recherche interactive de démonstration](quick-demo-map-app.md)
