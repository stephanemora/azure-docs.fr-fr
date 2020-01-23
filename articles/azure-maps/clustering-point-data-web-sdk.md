---
title: Clustering de données de point sur une carte | Microsoft Azure Maps
description: Dans cet article, vous allez apprendre à ajouter au cluster des données de points et à les afficher sur une carte à l’aide du SDK web Microsoft Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen
ms.openlocfilehash: 846abb61511ae1d5aedf77059ed2f1e9f4e5dbfb
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911737"
---
# <a name="clustering-point-data"></a>Clustering de données de point

Lorsque vous examinez plusieurs points de données sur la carte, les points se chevauchent et la carte semble surchargée, ce qui nuit à la visibilité et à l’utilisation. Le clustering de données de point peut servir à améliorer l’expérience utilisateur. Le clustering de point de données est le processus permettant de combiner des données de point proches les unes des autres et de les représenter sur une carte en tant qu’un point de données en cluster unique. Lorsque l’utilisateur effectue un zoom avant sur la carte, les clusters se décomposent pour afficher les points de données individuels qui les composent.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Clustering-point-data-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="enabling-clustering-on-a-data-source"></a>Activer le clustering sur une source de données

Le clustering peut facilement être activé sur la classe `DataSource` en définissant l’option `cluster` sur true. En outre, le rayon de pixel permettant de sélectionner les points proches à fusionner dans un cluster peut être défini à l’aide du paramètre `clusterRadius`. Le niveau de zoom auquel la logique de clustering est désactivée peut être spécifié à l’aide de l’option `clusterMaxZoom`. Voici un exemple montrant comment activer le clustering dans une source de données.

```javascript
//Create a data source and enable clustering.
var datasource = new atlas.source.DataSource(null, {
    //Tell the data source to cluster point data.
    cluster: true,

    //The radius in pixels to cluster points together.
    clusterRadius: 45,

    //The maximum zoom level in which clustering occurs.
    //If you zoom in more than this, all points are rendered as symbols.
    clusterMaxZoom: 15
});
```

> [!TIP]
> Si deux points de données sont proches sur le terrain, il est possible que le cluster ne se décompose jamais, peu importe le zoom avant effectué par l’utilisateur. Pour résoudre ce problème, vous pouvez définir l’option `clusterMaxZoom` de la source de données qui indique au niveau de zoom de désactiver la logique de clustering afin de simplement afficher tous les éléments.

La classe `DataSource` présente également les méthodes relatives au clustering suivantes :

| Méthode | Type de retour | Description |
|--------|-------------|-------------|
| getClusterChildren(clusterId: number) | Promise&lt;Array&lt;Feature&lt;Geometry, any&gt; \| Shape&gt;&gt; | Récupère les enfants du cluster donné sur le niveau de zoom suivant. Ces enfants peuvent être une combinaison de formes et sous-clusters. Les sous-clusters seront des fonctionnalités dont les propriétés correspondent à ClusteredProperties. |
| getClusterExpansionZoom(clusterId: number) | Promise&lt;number&gt; | Calcule un niveau de zoom à partir duquel le cluster commence à se développer ou à se décomposer. |
| getClusterLeaves(clusterId: number, limit: number, offset: number) | Promise&lt;Array&lt;Feature&lt;Geometry, any&gt; \| Shape&gt;&gt; | Récupère tous les points dans un cluster. Définissez le paramètre `limit` de manière à renvoyer un sous-ensemble des points et utilisez `offset` pour parcourir les points. |

## <a name="display-clusters-using-a-bubble-layer"></a>Afficher les clusters à l’aide d’une couche de bulles

Une couche de bulles est un excellent moyen pour afficher des points en cluster. Vous pouvez facilement mettre le rayon à l’échelle et modifier la couleur en fonction du nombre de points dans le cluster en utilisant une expression. Lorsque vous affichez des clusters sous la forme d’une couche de bulles, vous devez également utiliser une couche distincte pour le rendu des points de données hors du cluster. Il est souvent utile de pouvoir également afficher la taille du cluster en plus des bulles. Une couche de symbole comprenant du texte mais aucune icône peut aider à obtenir ce comportement. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Clustering en couche de bulles de base" src="//codepen.io/azuremaps/embed/qvzRZY/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consultez le rendu <a href='https://codepen.io/azuremaps/pen/qvzRZY/'>Clustering en couche de bulles de base</a> Pen par Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-clusters-using-a-symbol-layer"></a>Afficher les clusters à l’aide d’une couche de symboles

Lorsque vous visualisez des données de point à l’aide d’une couche de symboles, par défaut, les symboles qui se superposent sont masqués automatiquement afin de rendre l’expérience plus claire. Cependant, cette expérience peut ne pas vous convenir si vous souhaitez voir la densité des points de données sur la carte. Définir l’option `allowOverlap` de la propriété `iconOptions` des couches de symboles sur `true` permet de désactiver cette expérience, mais entraînera l’affichage de tous les symboles. À l’aide du clustering, vous pouvez voir la densité de toutes les données tout en créant une expérience utilisateur claire et agréable. Dans cet exemple, les symboles personnalisés seront utilisés pour représenter les clusters et les points de données individuels.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Couche de symboles en cluster" src="//codepen.io/azuremaps/embed/Wmqpzz/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consultez le rendu <a href='https://codepen.io/azuremaps/pen/Wmqpzz/'>Couche de symboles en cluster</a> Pen par Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="clustering-and-the-heat-maps-layer"></a>Clustering et couche de cartes thermiques

Les cartes thermiques sont un excellent moyen pour afficher la densité des données sur la carte. Cette visualisation suffit pour gérer un grand nombre de points de données, mais elle peut gérer davantage de données si les points de données sont mis en cluster et que la taille du cluster sert de poids pour la carte thermique. Pour cela, définissez l’option `weight` de la couche de carte thermique sur `['get', 'point_count']`. Lorsque le rayon de cluster est faible, la carte thermique ressemblera fortement à une carte thermique utilisant des points de données qui ne sont pas mis en cluster tout en étant plus efficace. Toutefois, la précision de la carte thermique augmente au détriment du gain en efficacité à mesure que le rayon de cluster diminue.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Carte thermique pondérée en fonction du cluster" src="//codepen.io/azuremaps/embed/VRJrgO/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consultez le rendu <a href='https://codepen.io/azuremaps/pen/VRJrgO/'>Carte thermique pondérée en fonction du cluster</a> Pen par Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="mouse-events-on-clustered-data-points"></a>Événements de souris sur des points de données en cluster

Lorsque des événements de souris se produisent sur une couche contenant des points de données en cluster, ces derniers sont retournés à l’événement en tant qu’objet de fonctionnalité de point GeoJSON. Cette fonctionnalité de point possède les propriétés suivantes :

| Nom de la propriété             | Type    | Description   |
|---------------------------|---------|---------------|
| `cluster`                 | boolean | Indique si la fonctionnalité représente un cluster. |
| `cluster_id`              | string  | Un ID unique pour le cluster qui peut être utilisé avec les méthodes `getClusterExpansionZoom`, `getClusterChildren` et `getClusterLeaves` de DataSource. |
| `point_count`             | nombre  | Le nombre de points que contient le cluster.  |
| `point_count_abbreviated` | string  | Une chaîne qui abrège la valeur `point_count` si elle est trop longue. (par exemple, 4 000 devient 4K)  |

Cet exemple utilise une couche de bulles qui affiche des points de cluster et ajoute un événement basé sur un clic. Lorsqu’il est déclenché, il calcule et effectue un zoom sur la carte jusqu’au prochain niveau de zoom provoquant une décomposition du cluster à l’aide de la méthode `getClusterExpansionZoom` de la classe `DataSource` et de la propriété `cluster_id` du point de données en cluster sur lequel l’utilisateur a cliqué. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Méthode getClusterExpansionZoom du cluster" src="//codepen.io/azuremaps/embed/moZWeV/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consultez le rendu <a href='https://codepen.io/azuremaps/pen/moZWeV/'>Méthode getClusterExpansionZoom du cluster</a> Pen par Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-cluster-area"></a>Afficher la zone du cluster 

Les données de point représentées par un cluster sont réparties sur une zone. Dans cet exemple, une action est déclenchée lorsque la souris survole un cluster. Les points de données individuels contenus (laissés) dans le cluster sont alors utilisés pour calculer une enveloppe convexe et affichés sur la carte pour montrer la zone. Tous les points contenus dans un cluster peuvent être récupérés à partir de la source de données à l’aide de la méthode `getClusterLeaves`. Une enveloppe convexe est un polygone qui entoure un ensemble de points comme un élastique et qui peut être calculé à l’aide de la méthode `atlas.math.getConvexHull`.

<br/>

 <iframe height="500" style="width: 100%;" scrolling="no" title="Enveloppe convexe de la zone du cluster" src="//codepen.io/azuremaps/embed/QoXqWJ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consultez le rendu <a href='https://codepen.io/azuremaps/pen/QoXqWJ/'>Enveloppe convexe de la zone du cluster</a> Pen par Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="aggregating-data-in-clusters"></a>Agrégation de données dans des clusters

Souvent, les clusters sont représentés à l’aide d’un symbole avec le nombre de points qui se trouvent dans le cluster. Toutefois, il est parfois souhaitable de personnaliser davantage le style des clusters en fonction d’un indicateur de performance, comme le total de tous les points au sein d’un cluster. Avec les agrégats de cluster, les propriétés personnalisées peuvent être créées et remplies à l’aide d’un calcul [d’expression d’agrégation](data-driven-style-expressions-web-sdk.md#aggregate-expression).  Les agrégats de cluster peuvent être définis dans l'option `clusterProperties` de `DataSource`.

L’exemple suivant utilise une expression d’agrégation pour calculer un nombre basé sur la propriété de type d’entité de chaque point de données dans un cluster.

<iframe height="500" style="width: 100%;" scrolling="no" title="Agrégats de cluster" src="//codepen.io/azuremaps/embed/jgYyRL/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consultez le <a href='https://codepen.io/azuremaps/pen/jgYyRL/'>Agrégats de cluster</a> Pen par Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les classes et les méthodes utilisées dans cet article :

> [!div class="nextstepaction"]
> [Classe DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Objet DataSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Espace de noms atlas.math](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)

Consultez les exemples de code pour ajouter la fonctionnalité à votre application :

> [!div class="nextstepaction"]
> [Ajouter une couche de bulles](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Ajouter une couche de symboles](map-add-pin.md)

> [!div class="nextstepaction"]
> [Ajouter une couche de carte thermique](map-add-heat-map-layer.md)
