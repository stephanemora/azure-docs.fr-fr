---
title: Clustering de données du point en Azure Maps | Microsoft Docs
description: Comment mettre en cluster les données de point dans le Kit de développement Web
author: rbrundritt
ms.author: richbrun
ms.date: 03/27/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: d4dc6f0c8fd2dff74a1997c9dca5a31abc70c03a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60795935"
---
# <a name="clustering-point-data"></a>Clustering de point de données

Lorsque vous examinez le nombre de points de données sur la carte, points chevauchent, le mappage de recherche confu, et il devient difficile de voir et utiliser. Clustering de point de données peut servir à améliorer cette expérience utilisateur. Clustering de point de données est le processus de combinaison de point de données qui est proches les uns des autres et qui les représentent sur la carte en tant qu’un point de données en cluster. Comme l’utilisateur effectue un zoom avant dans la classe map, les clusters décomposer en leurs points de données individuels.

## <a name="enabling-clustering-on-a-data-source"></a>Activer le clustering sur une source de données

Clustering peut facilement être activé sur le `DataSource` classe en définissant le `cluster` option sur true. En outre, le rayon de pixel pour sélectionner les points proches pour placer dans un cluster peut être défini à l’aide de la `clusterRadius` et un niveau de zoom peut être spécifié désactiver le clustering à l’aide de logique auquel le `clusterMaxZoom` option. Voici un exemple montrant comment activer le clustering dans une source de données.

```javascript
//Create a data source and enable clustering.
var datasource = new atlas.source.DataSource(null, {
    //Tell the data source to cluster point data.
    cluster: true,

    //The radius in pixels to cluster points together.
    clusterRadius: 45,

    //The maximium zoom level in which clustering occurs.
    //If you zoom in more than this, all points are rendered as symbols.
    clusterMaxZoom: 15 
});
```

> [!TIP]
> Si deux points de données sont proches sur le terrain, il est possible du que cluster sera jamais décomposer, peu importe comment fermer l’utilisateur effectue un zoom avant dans. Pour résoudre ce problème, vous pouvez définir le `clusterMaxZoom` option de la source de données qui spécifie au niveau de zoom pour désactiver la logique de clustering et de simplement afficher tous les éléments.

Le `DataSource` classe a également les méthodes suivantes de clustering :

| Méthode | Type de retour | Description |
|--------|-------------|-------------|
| getClusterChildren(clusterId: number) | Promesse&lt;fonctionnalité&lt;géométrie, n’importe quel&gt; \| forme&gt; | Récupère les enfants du cluster donné sur le niveau de zoom suivant. Ces enfants peuvent être une combinaison de formes et subclusters. Les subclusters seront les fonctionnalités dont les propriétés correspondent ClusteredProperties. |
| getClusterExpansionZoom(clusterId: number) | Promesse&lt;nombre&gt; | Calcule un niveau de zoom à partir duquel le cluster ne démarrera développant ou décomposer. |
| getClusterLeaves(clusterId: number, limit: number, offset: number) | Promesse&lt;fonctionnalité&lt;géométrie, n’importe quel&gt; \| forme&gt; | Récupère tous les points dans un cluster. Définir le `limit` pour renvoyer un sous-ensemble des points et le `offset` pour passer en revue les points. |

## <a name="display-clusters-using-a-bubble-layer"></a>Afficher les clusters à l’aide d’une couche à bulles

Une couche à bulles est un excellent moyen pour restituer des points en cluster que vous pouvez facilement mettre à l’échelle le rayon et modifier la couleur que les selon le nombre de points dans le cluster en utilisant une expression. Lors de l’affichage des clusters à l’aide d’une couche à bulles, vous devez également utiliser une couche distincte pour le rendu des points de données sans cluster. Il est souvent utile de pouvoir également afficher la taille du cluster sur les bulles. Une couche de symbole avec aucune icône et le texte peut être utilisée pour obtenir ce comportement. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Couche à bulles base de clustering" src="//codepen.io/azuremaps/embed/qvzRZY/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consultez le stylet <a href='https://codepen.io/azuremaps/pen/qvzRZY/'>clustering de couche à bulles base</a> par Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-clusters-using-a-symbol-layer"></a>Afficher les clusters à l’aide d’une couche de symbole

Lors de la visualisation des données de point à l’aide de la couche de symbole par défaut qu’il se masque automatiquement les symboles qui se chevauchent mutuellement pour créer une expérience plus propre, cependant cette peut-être pas l’expérience souhaitée si vous souhaitez voir la densité de données pointe sur la carte. Définition de la `allowOverlap` option des couches de symbole `iconOptions` propriété `true` désactive cette expérience, mais entraîne dans tous les symboles affichés. À l’aide du clustering permet de vous permet de voir la densité de toutes les données lors de la création d’une expérience utilisateur propre attrayante. Dans cet exemple, les symboles personnalisés seront utilisés pour représenter des clusters et les points de données individuels.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Couche de symbole en cluster" src="//codepen.io/azuremaps/embed/Wmqpzz/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consultez le stylet <a href='https://codepen.io/azuremaps/pen/Wmqpzz/'>couche de symbole en cluster</a> par Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="clustering-and-the-heat-maps-layer"></a>Clustering et la chaleur mappe la couche

Cartes thermiques sont un excellent moyen pour afficher la densité des données sur la carte. Cette visualisation peut gérer un grand nombre de points de données en soi, mais il peut gérer davantage de données si les points de données sont en cluster et la taille du cluster est utilisée en tant que le poids de la carte thermique. Définir le `weight` option de la couche de carte thermique pour `['get', 'point_count']` pour y parvenir. Lorsque le rayon de cluster est faible, la carte thermique ressemblera presque identique à une carte thermique en utilisant les points de données sans cluster, mais beaucoup plus efficace. Toutefois, plus le rayon de cluster, la plus précise la carte thermique est mais avec moins d’une performance bénéficier.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Cluster pondérée carte thermique" src="//codepen.io/azuremaps/embed/VRJrgO/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consultez le stylet <a href='https://codepen.io/azuremaps/pen/VRJrgO/'>Cluster pondérée carte thermique</a> par Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="mouse-events-on-clustered-data-points"></a>Événements de souris sur les points de données en cluster

Lorsque des événements de souris se produisent sur une couche qui contiennent des points de données en cluster, le point de données en cluster sera retourné à l’événement comme un objet de fonction de point de GeoJSON. Cette fonctionnalité point a les propriétés suivantes :

| Nom de la propriété | type | Description |
|---------------|------|-------------|
| cluster | booléenne | Indique si les fonctionnalité représente un cluster. |
| cluster_id | string | Un ID unique pour le cluster peut être utilisé avec la source de données `getClusterExpansionZoom`, `getClusterChildren`, et `getClusterLeaves` méthodes. |
| point_count | number | Le nombre de points que contient le cluster. |
| point_count_abbreviated | string | Chaîne qui abrège la valeur point_count si elle est long. (par exemple, 4 000 devient 4K) |

Cet exemple accepte une couche à bulles qui restitue des points de cluster et ajoute un événement click qui déclenchement, calculer, et effectuer un zoom de la carte au niveau de zoom suivant à partir duquel le cluster s’arrête les unes des autres à l’aide de la `getClusterExpansionZoom` méthode de la `DataSource` classe et le `cluster_id` propriété de l’utilisateur a cliqué dessus en cluster le point de données. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Cluster getClusterExpansionZoom" src="//codepen.io/azuremaps/embed/moZWeV/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consultez le stylet <a href='https://codepen.io/azuremaps/pen/moZWeV/'>Cluster getClusterExpansionZoom</a> par Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-cluster-area"></a>Zone d’affichage de cluster 

Les données du point qui représente un cluster sont réparties sur une zone. Dans cet exemple, lorsque la souris se trouve sur un cluster, les données individuelles points qu’il contient (feuilles) seront utilisées pour calculer une enveloppe convexe et affichés sur la carte pour afficher la zone. Tous les points contenus dans un cluster peuvent être récupérées à partir de la source de données à l’aide du `getClusterLeaves` (méthode). Une forme convexe est un polygone qui encapsule un ensemble de points comme une bande élastique et peut être calculée à l’aide de la `atlas.math.getConvexHull` (méthode).

<br/>

 <iframe height="500" style="width: 100%;" scrolling="no" title="Forme convexe de zone cluster" src="//codepen.io/azuremaps/embed/QoXqWJ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consultez le stylet <a href='https://codepen.io/azuremaps/pen/QoXqWJ/'>forme convexe de Cluster zone</a> par Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) sur <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les classes et les méthodes utilisées dans cet article :

> [!div class="nextstepaction"]
> [Classe de source de données](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Objet de DataSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [espace de noms Atlas.Math](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)

Consultez les exemples de code pour ajouter la fonctionnalité à votre application :

> [!div class="nextstepaction"]
> [Ajouter une couche de bulles](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Ajouter une couche de symboles](map-add-pin.md)

> [!div class="nextstepaction"]
> [Ajouter une couche de carte thermique](map-add-heat-map-layer.md)
