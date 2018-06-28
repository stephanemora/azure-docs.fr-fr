---
title: Vue d’ensemble d’Azure Maps | Microsoft Docs
description: Présentation d’Azure Maps
author: kgremban
ms.author: kgremban
ms.date: 05/07/2018
ms.topic: overview
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 239134e7665b80ef78b6a3df12c14156bff9ae29
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/22/2018
ms.locfileid: "36317056"
---
# <a name="an-introduction-to-azure-maps"></a>Présentation d’Azure Maps
Azure Maps est un portefeuille de services géospatiaux comprenant différentes API de services : cartes, recherche, création d’itinéraires, trafic et fuseaux horaires. Le portefeuille de services vous permet d’utiliser vos outils habituels pour développer et mettre à l’échelle rapidement des solutions Azure intégrant des informations de localisation. Azure Maps offre aux développeurs de tous les secteurs de puissantes fonctionnalités géospatiales, avec les données de cartographie actualisées nécessaires pour fournir un contexte géographique à des applications mobiles et web. Cet ensemble d’API REST s’accompagne d’un contrôle JavaScript web, qui garantit un développement simplifié, flexible et portable sur plusieurs supports. 

La vidéo suivante présente Azure Maps :

<iframe src="https://channel9.msdn.com/Shows/Azure-Friday/Azure-Location-Based-Services/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

Azure Maps est constitué de cinq services principaux, qui viennent compléter les applications Azure pour lesquelles un contexte géographique est nécessaire. Chacun de ces services est expliqué en détail.

**Render Service** permet aux développeurs de créer des applications web et mobiles autour de la cartographie. Le service utilise des images graphiques raster de haute qualité disponibles en 19 niveaux de zoom ou des images de cartes au format vectoriel entièrement personnalisables.

![Azure Maps Map.png](media/about-azure-maps/Introduction_Map.png)

**Route Service** comporte des fonctionnalités robustes et tangibles de calcul géométrique d’infrastructure, et différentes instructions de modes de transport. Le service permet aux développeurs de calculer des directions pour plusieurs modes de déplacement, notamment en voiture, en camion, à vélo ou à pied. Le service peut également prendre en compte d’autres entrées, par exemple, les conditions de circulation, les restrictions de poids et le transport de matières dangereuses.

![Azure Maps Route.png](media/about-azure-maps/Introduction_Route.png)

**Search Service** permet aux développeurs de rechercher des adresses, des lieux, des listes d’entreprises par nom ou catégorie, et d’autres informations d’ordre géographique. Search Service peut aussi [géocoder en inversé](https://en.wikipedia.org/wiki/Reverse_geocoding) les adresses et rues de croisement en fonction d’une latitude/longitude. 

![Azure Maps Search.png](media/about-azure-maps/Introduction_Search.png)

**Time Zone Service** permet d’interroger des informations actuelles, historiques et futures sur un fuseau horaire à l’aide de paires latitude-longitude ou d’un [ID IANA](http://www.iana.org/). Time Zone Service permet aussi de convertir les ID de fuseau horaire Microsoft Windows en fuseaux horaires IANA, de récupérer un décalage de fuseau horaire par rapport à UTC et d’obtenir l’heure actuelle dans un fuseau horaire respectif. Voici un exemple de réponse JSON type à une requête Time Zone Service :

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

**Traffic Service** est une suite de services web permettant aux développeurs de créer des applications web et mobiles pour lesquelles des informations sur le trafic sont nécessaires. Ce service fournit deux types de données :
* flux de trafic : vitesses et durées de déplacement observées en temps réel pour toutes les routes clés du réseau ; 
* incidents de trafic : image précise des embouteillages et des incidents sur le réseau routier.

![Trafic Azure Maps](media/about-azure-maps/Introduction_Traffic.png)

Conçu pour la mobilité, Azure Maps peut être utilisé par des applications multiplateformes ; en effet, le modèle de programmation est agnostique et prend en charge la sortie JSON grâce à des API REST. En outre, Azure Maps propose un contrôle de carte JavaScript pratique avec un modèle de programmation simple, ce qui facilite le développement d’applications web et mobiles. 

Azure Maps utilise un schéma d’authentification basé sur les clés ; l’accès aux services se fait donc en accédant au [Portail Azure](http://portal.azure.com) et en créant un compte Azure Maps. Votre compte est doté de deux clés prégénérées pour vous. Vous pouvez intégrer directement ces fonctionnalités de localisation dans vos applications en utilisant l’une de vos clés dans les requêtes envoyées au service Azure Maps.

## <a name="unsupported-regions"></a>Régions non prises en charge
À l’heure actuelle, l’API Azure Maps n’est pas disponible dans tous les pays. Contrôlez votre adresse IP actuelle, et vérifiez que son emplacement ne fait pas partie des pays non pris en charge de la liste ci-dessous :

* Argentine
* Chine
* Inde
* Maroc
* Pakistan
* Corée du Sud

## <a name="next-steps"></a>Étapes suivantes

Vous avez à présent une vue d’ensemble d’Azure Maps. Maintenant, testez un exemple d’application qui présente le service.

> [!div class="nextstepaction"]
> [Lancer une carte de recherche interactive de démonstration](quick-demo-map-app.md)
