---
title: Questions fréquentes (FAQ) sur les services Météo de Microsoft Azure Maps
description: Trouvez des réponses aux questions les plus fréquentes sur les données et les fonctionnalités des services Météo d'Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 440ff94f4d5f3514d97d3e76299b60e462cebff3
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107257549"
---
# <a name="azure-maps-weather-services-frequently-asked-questions-faq"></a>Questions fréquentes (FAQ) sur les services Météo d'Azure Maps

Cet article répond aux questions les plus fréquentes sur les données et les fonctionnalités des [services météo](/rest/api/maps/weather) Azure Maps. Les rubriques suivantes sont traitées :

* Sources de données et modèles de données
* Couverture et disponibilité des services météo
* Fréquence de mise à jour des données
* Développer avec les SDK Azure Maps
* Options pour visualiser les données météo, y compris l’intégration Microsoft Power BI

## <a name="data-sources-and-data-models"></a>Sources de données et modèles de données

**Comment Azure Maps se procure-t-il les données météorologiques ?**

Azure Maps est basé sur la collaboration de plusieurs partenaires spécialisés en technologies de mobilité et de localisation, notamment AccuWeather, qui fournit les données météorologiques sous-jacentes. Pour lire l’annonce relative à la collaboration entre Azure Maps et AccuWeather, consultez [Pluie ou soleil : Les services météorologiques Azure Maps fourniront des insights à votre entreprise](https://azure.microsoft.com/blog/rain-or-shine-azure-maps-weather-services-will-bring-insights-to-your-enterprise/).

AccuWeather dispose d’informations météorologiques et environnementales en temps réel qui sont disponibles partout dans le monde, principalement grâce à leurs partenariats avec de nombreuses agences météorologiques nationales et d’autres organisations privées. Vous trouverez ci-dessous la liste de ces informations fondamentales.

* Observations de surface mondiales provenant d’organismes publics et disponibles pour tous
* Jeux de données d’observations de surface propriétaires provenant d’organismes publics et de sociétés privées
* Données radar haute résolution pour plus de 40 pays ou régions
* Données mondiales en temps réel sur les éclairs
* Alertes météo émises par les organismes publics pour plus de 60 pays, régions et territoires
* Données satellites provenant de satellites géostationnaires couvrant l’ensemble de la planète
* Plus de 150 modèles de prévision numérique, notamment les modèles internes propriétaires, les modèles publics tels que les modèles GFS utilisés aux États-Unis et les modèles haute résolution uniques fournis par des sociétés privées
* Observations sur la qualité de l’air
* Observations des services de transport

Des dizaines de milliers d’observations de surface, ainsi que d’autres données, sont incorporées pour créer et influencer les conditions actuelles mises à la disposition des utilisateurs. Cela inclut non seulement les jeux de données standard disponibles gratuitement, mais également les observations uniques fournies par les services météorologiques nationaux de nombreux pays, y compris l’Inde, le Brésil et le Canada, ainsi que d’autres entrées propriétaires. Ces jeux de données uniques augmentent la résolution spatiotemporelle des données de conditions actuelles pour nos utilisateurs. 

Ces jeux de données sont examinés en temps réel par souci de justesse pour le système de prévision numérique, qui utilise les algorithmes d’intelligence artificielle propriétaires d’AccuWeather pour modifier continuellement les prévisions, ce qui garantit leur justesse.

**Quels modèles créent des données de prévision météorologiques ?**

De nombreux systèmes de prévisions météorologiques sont utilisés pour formuler des prévisions mondiales. Plus de 150 modèles de prévision numérique sont utilisés chaque jour, à la fois dans des jeux de données externes et internes. Cela comprend les modèles publics tels que le modèle européen ECMWF et le modèle américain Global Forecast System (GFS). De plus, AccuWeather intègre des modèles haute résolution propriétaires qui concentrent les prévisions sur des régions spécifiques et des domaines régionaux stratégiques afin de prédire le temps avec davantage de précision. Les algorithmes de fusion et de pondération uniques d’AccuWeather ont été développés sur plusieurs décennies. Ces algorithmes tirent pleinement parti des nombreuses entrées de prévision pour fournir des prévisions avec un haut niveau de justesse.

## <a name="weather-services-coverage-and-availability"></a>Couverture et disponibilité des services météo

**Quel type de couverture puis-je attendre pour différents pays ou régions ?**

La couverture du service Météo varie selon le pays ou la région. Certaines fonctionnalités ne sont pas disponibles dans tous les pays ou régions. Pour plus d’informations, consultez la [documentation relative à la couverture](./weather-coverage.md).

## <a name="data-update-frequency"></a>Fréquence de mise à jour des données

**À quelle fréquence les données des conditions actuelles sont mises à jour ?**

Les données des conditions actuelles sont mises à jour au moins une fois par heure. Cependant, elles peuvent être mises à jour plus fréquemment en cas de changement brusque (température, état du ciel, précipitations, etc.). La plupart des stations d’observation du monde fournissent les données qu’elles collectent de nombreuses fois par heure, à mesure que les conditions changent. Toutefois, il existe un nombre limité de régions qui sont mises à jour uniquement entre une seule et quatre fois par heure, à des intervalles planifiés.  

Azure Maps met en cache les données des conditions actuelles pendant une période allant jusqu’à 10 minutes, afin d’aider à capturer la fréquence de mise à jour en quasi-temps réel des données. Pour voir à quel moment la réponse mise en cache expire et éviter d’afficher des données obsolètes, vous pouvez utiliser les informations d’en-tête Expires situées dans l’en-tête HTTP de la réponse de l’API Azure Maps.

**À quelle fréquence les données des prévisions quotidiennes et heure par heure sont mises à jour ?**

Les données des prévisions quotidiennes et heure par heure sont mises à jour plusieurs fois par jour, à mesure que de nouvelles observations sont reçues.  Par exemple, si la température maximale ou minimale prévue est dépassée, nos données de prévision s’ajusteront lors du prochain cycle de mise à jour. Cela peut se produire à différents intervalles, mais en général, cela se produit dans l’heure. De nombreuses conditions météorologiques soudaines peuvent entraîner la modification des données de prévision. Par exemple, lors d’un après-midi d’été particulièrement chaud, un orage isolé peut soudain se produire et apporter avec lui des nuages et de la pluie. L’orage peut provoquer une baisse des températures allant jusqu’à 10 degrés. Cette nouvelle valeur de température aura un impact sur les prévisions quotidiennes et heure par heure pour le reste de la journée et fera donc l’objet d’une mise à jour dans nos jeux de données.

Les API de prévisions météo Azure Maps sont mises en cache pendant une période allant jusqu’à 30 minutes. Pour voir à quel moment la réponse mise en cache expire et éviter d’afficher des données obsolètes, vous pouvez utiliser les informations d’en-tête Expires situées dans l’en-tête HTTP de la réponse de l’API Azure Maps. Nous vous recommandons de procéder aux mises à jour nécessaires en fonction du cas d’usage ou de l’interface utilisateur du produit.

## <a name="developing-with-azure-maps-sdks"></a>Développer avec les SDK Azure Maps

**Le SDK Web d'Azure Maps prend-il en charge l'intégration des services Météo de manière native ?**

Le Kit de développement logiciel (SDK) web Azure Maps fournit un module des services. Le module des services est une bibliothèque d’assistance qui facilite l’utilisation des services REST Azure Maps dans des applications web ou Node.js avec JavaScript ou TypeScript. Pour bien démarrer, consultez notre [documentation](./how-to-use-services-module.md).

**Le SDK Android d'Azure Maps prend-il en charge l'intégration des services Météo de manière native ?**

Les SDK Android Azure Maps prennent en charge les calques de vignettes Mercator, qui peuvent avoir une notation x/y/zoom, une notation Quadkey ou une notation de cadre englobant EPSG 3857.

Nous prévoyons de créer un module de services pour Java/Android qui sera similaire au module du SDK web. Le module des services Android facilite l’accès à tous les services Azure Maps au sein d’une application Java ou Android.  

## <a name="data-visualizations"></a>Visualisations de données  

**Les visuels Power BI Azure Maps prennent-ils en charge les vignettes météo Azure Maps ?**

Oui. Pour savoir comment effectuer la migration des vignettes radar et satellite infrarouge vers un visuel Microsoft Power BI, consultez [Ajouter un calque de vignettes à un visuel Power BI](./power-bi-visual-add-tile-layer.md). 

**Comment interpréter les couleurs utilisées pour les vignettes radar et satellite ?**

L’article Azure Maps concernant les [concepts relatifs au service Météo](./weather-services-concepts.md#radar-and-satellite-imagery-color-scale) explique comment interpréter les couleurs qui sont utilisées pour les vignettes radar et satellite. L’article montre des exemples de couleurs et de codes couleur HEX.
 
**Puis-je créer des animations radar et satellite ?**

Oui. En plus des vignettes radar et satellite en temps réel, les utilisateurs d’Azure Maps peuvent demander des vignettes passées et à venir afin d’améliorer les visualisations de données avec des superpositions de carte. Pour cela, vous pouvez appeler directement [l’API Get Map Tile v2](/rest/api/maps/renderv2/getmaptilepreview) ou demander des vignettes via le SDK web Azure Maps. Vous pouvez obtenir des vignettes radar passées avec une ancienneté maximale d’une heure et demi, et des vignettes radar à venir avec une projection maximale de deux heures dans le temps. Ces vignettes sont mises à disposition toutes les 5 minutes. Vous pouvez obtenir des vignettes infrarouges passées avec une ancienneté maximale de 3 heures. En outre, ces vignettes sont mises à disposition toutes les 10 minutes. Pour plus d’informations, consultez l’[exemple de code](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Animated%20tile%20layer) de l’animation sur les vignettes météo open source.  

**Mettez-vous à disposition des icônes pour les différentes conditions météorologiques ?**

Oui. Les icônes et leur code sont [disponibles ici](./weather-services-concepts.md#weather-icons). Notez que seules certaines des API du service Météo (préversion), comme l’[API Get Current Conditions](/rest/api/maps/weather/getcurrentconditions), retournent l’*iconCode* dans la réponse. Pour plus d’informations, consultez l’[exemple de code](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Get%20current%20weather%20at%20a%20location) open source « Current Weather Conditions ».

## <a name="next-steps"></a>Étapes suivantes

Si les Questions fréquentes ne répondent pas à votre question, vous pouvez nous joindre par le biais des méthodes suivantes (par ordre de priorité) :

* La section Commentaires de cet article
* [Page Questions fréquentes MSFT pour Azure Maps](/answers/topics/azure-maps.html).
* Support Microsoft Pour créer une demande de support, dans le portail Azure, sous l’onglet [Aide](https://portal.azure.com/), sélectionnez le bouton **Aide et support**, puis **Nouvelle demande de support**.
* [Azure Maps UserVoice](https://feedback.azure.com/forums/909172-azure-maps) pour effectuer des demandes de fonctionnalités.

Apprenez à effectuer des demandes de données météorologiques en temps réel et des demandes de prévisions à l'aide des services Météo d'Azure Maps :
> [!div class="nextstepaction"]
> [Demander des données météorologiques en temps réel](how-to-request-weather-data.md)

Articles sur les concepts des services Météo d'Azure Maps :
> [!div class="nextstepaction"]
> [Concepts des services Météo](weather-coverage.md)

Explorez la documentation relative à l'API des services Météo d'Azure Maps :

> [!div class="nextstepaction"]
> [Services météo Azure Maps](/rest/api/maps/weather)