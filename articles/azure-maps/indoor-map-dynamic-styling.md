---
title: Implémenter des styles dynamiques pour les cartes d’intérieur du Créateur Azure Maps
description: Découvrez comment implémenter des styles dynamiques pour les cartes d’intérieur du Créateur
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/20/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: 05e169667067033428d5fc995af4d866dc46d20b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122525319"
---
# <a name="implement-dynamic-styling-for-creator-indoor-maps"></a>Implémenter des styles dynamiques pour les cartes d’intérieur du Créateur

Vous pouvez utiliser le [service État de la fonctionnalité](/rest/api/maps/v2/feature-state) d’Azure Maps Creator pour appliquer des styles basés sur les propriétés dynamiques des caractéristiques des données cartographiques intérieures.  Par exemple, vous pouvez afficher des salles de réunion d’un bâtiment dans une couleur spécifique reflétant leur état d’occupation. Cet article explique comment afficher de façon dynamique des caractéristiques de carte d’intérieur avec le [service État de la fonctionnalité](/rest/api/maps/v2/feature-state) et le [module web Intérieur](how-to-use-indoor-module.md).

## <a name="prerequisites"></a>Prérequis

1. [Créer un compte Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Obtenir une clé d’abonnement principale](quick-demo-map-app.md#get-the-primary-key-for-your-account), également appelée clé primaire ou clé d’abonnement.
3. [Créer une ressource de Créateur](how-to-manage-creator.md)
4. Téléchargez l’[exemple de package de dessin](https://github.com/Azure-Samples/am-creator-indoor-data-examples).
5. [Créez une carte d’intérieur](tutorial-creator-indoor-maps.md) pour obtenir un ID `tilesetId` et un ID `statesetId`.
6. Générez une application web en suivant les étapes décrites dans [comment utiliser le module Cartes d’intérieur](how-to-use-indoor-module.md).

Ce tutoriel utilise l’application [Postman](https://www.postman.com/), mais vous pouvez choisir un autre environnement de développement d’API.

## <a name="implement-dynamic-styling"></a>Implémenter l’application de style dynamique

Après avoir rempli les conditions préalables, vous devez disposer d’une application web simple configurée avec votre clé d’abonnement et vos identificateurs `tilesetId` et `statesetId`.

### <a name="select-features"></a>Sélectionner les caractéristiques

Pour implémenter l’application de style dynamique, une caractéristique, telle qu’une salle de réunion ou de conférence, doit être référencée par sa caractéristique `id`. Vous utilisez la caractéristique `id` pour mettre à jour la propriété dynamique ou l’*état* de cette caractéristique. Pour afficher les fonctionnalités définies dans un jeu de données, vous pouvez utiliser l’une des méthodes suivantes :

* API WFS (service de caractéristique web). Vous pouvez utiliser l’[API WFS](/rest/api/maps/v2/wfs) pour interroger des jeux de données. L’API de service de caractéristique web suit les [caractéristiques de l’API Open Geospatial Consortium](http://docs.opengeospatial.org/DRAFTS/17-069r1.html). L’API de service de caractéristique web est utile pour interroger des caractéristiques à l’intérieur d’un jeu de données. Par exemple, vous pouvez utiliser WFS pour rechercher toutes les salles de réunion de taille moyenne d’un bâtiment et d’un étage particuliers.

* Implémentez du code personnalisé qu’un utilisateur peut utiliser pour sélectionner des caractéristiques sur une carte à l’aide de votre application web. Nous utilisons cette option dans cet article.  

Le script suivant implémente l’événement de clic de souris. Le code récupère la caractéristique `id` en fonction du point cliqué. Dans votre application, vous pouvez insérer le code après le bloc de code du gestionnaire d’intérieur. Exécutez votre application, puis consultez la console pour obtenir l’`id` de caractéristique du point cliqué.

```javascript
/* Upon a mouse click, log the feature properties to the browser's console. */
map.events.add("click", function(e){

    var features = map.layers.getRenderedShapes(e.position, "indoor");

    features.forEach(function (feature) {
        if (feature.layer.id == 'indoor_unit_office') {
            console.log(feature);
        }
    });
});
```

Le tutoriel [Créer une carte d’intérieur](tutorial-creator-indoor-maps.md) a configuré le stateset de caractéristique pour accepter les mises à jour d’état pour `occupancy`.

Dans la section suivante, nous allons définir l’*état* d’occupation du bureau `UNIT26` sur `true` et du bureau `UNIT27` sur `false`.

### <a name="set-occupancy-status"></a>Définir l’état d’occupation

 Nous allons maintenant mettre à jour l’état des deux bureaux, `UNIT26` et `UNIT27` :

1. Dans l’application Postman, sélectionnez **New** (Nouveau).

2. Dans la fenêtre **Create New** (Créer), sélectionnez **HTTP Request** (Requête HTTP).

3. Entrez un **Request name** (Nom de demande) pour la demande, par exemple *POST Data Upload*.

4. Entrez l’URL suivante pour l’[API des états de mise à jour des fonctionnalités](/rest/api/maps/v2/feature-state/update-states) (remplacez `{Azure-Maps-Primary-Subscription-key}` par votre clé d’abonnement principale et `statesetId` par le `statesetId`) :

    ```http
    https://us.atlas.microsoft.com/featurestatesets/{statesetId}/featureStates/UNIT26?api-version=2.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

5. Sélectionnez l’onglet **En-têtes**.

6. Dans le champ **KEY** (CLÉ), sélectionnez `Content-Type`. Dans le champ **VALUE** (VALEUR), sélectionnez `application/json`.

     :::image type="content" source="./media/indoor-map-dynamic-styling/stateset-header.png"alt-text="Informations sur l’onglet En-têtes pour la création de stateset.":::

7. Sélectionnez l’onglet **Corps** .

8. Dans les listes déroulantes, sélectionnez **raw** (brut) et **JSON**.

9. Copiez le style JSON suivant, puis collez-le dans la fenêtre **Body** (Corps) :

    ```json
    {
        "states": [
            {
                "keyName": "occupied",
                "value": true,
                "eventTimestamp": "2020-11-14T17:10:20"
            }
        ]
    }
    ```

    >[!IMPORTANT]
    >La mise à jour n’est enregistrée que si l’horodatage de publication est postérieur à l’horodatage utilisé dans les requêtes de mise à jour d’état de caractéristique précédentes pour le même `ID` de caractéristique.

10. Modifiez l’URL que vous avez utilisée à l’étape 7 en remplaçant `UNIT26` par `UNIT27` :

    ```http
    https://us.atlas.microsoft.com/featurestatesets/{statesetId}/featureStates/UNIT27?api-version=2.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

11. Copiez le style JSON suivant, puis collez-le dans la fenêtre **Body** (Corps) :

    ``` json
    {
        "states": [
            {
                "keyName": "occupied",
                "value": false,
                "eventTimestamp": "2020-11-14T17:10:20"
            }
        ]
    }
    ```

### <a name="visualize-dynamic-styles-on-a-map"></a>Visualiser des styles dynamiques sur une carte

L’application web que vous avez précédemment ouverte dans un navigateur doit maintenant refléter l’état mis à jour des caractéristiques de carte :
- Le bureau `UNIT27` (142) doit apparaître en vert.
- Le bureau `UNIT26` (143) doit apparaître en rouge.

![Salle libre en vert et salle occupée en rouge](./media/indoor-map-dynamic-styling/room-state.png)

[Voir la démonstration en direct](https://azuremapscodesamples.azurewebsites.net/?sample=Creator%20indoor%20maps)

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus, consultez :

> [!div class="nextstepaction"]
> [Créateur pour cartes d’intérieur](creator-indoor-maps.md)

Consultez les références des API mentionnées dans cet article :

> [!div class="nextstepaction"]
> [Chargement de données](creator-indoor-maps.md#upload-a-drawing-package)

> [!div class="nextstepaction"]
> [Conversion de données](creator-indoor-maps.md#convert-a-drawing-package)

> [!div class="nextstepaction"]
> [Dataset](creator-indoor-maps.md#datasets)

> [!div class="nextstepaction"]
> [Tileset](creator-indoor-maps.md#tilesets)

> [!div class="nextstepaction"]
> [Jeu d’état de caractéristique](creator-indoor-maps.md#feature-statesets)

> [!div class="nextstepaction"]
> [Service de caractéristique web](creator-indoor-maps.md#web-feature-service-api)
