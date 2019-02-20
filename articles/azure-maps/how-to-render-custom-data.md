---
title: Comment afficher des données personnalisées sur une carte raster dans Azure Maps | Microsoft Docs
description: Affichage de données personnalisées sur une carte raster dans Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 9b3c0f7b1ff56cb269f6852be8fd2affeca8b8f1
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/12/2019
ms.locfileid: "56119073"
---
# <a name="render-custom-data-on-raster-map"></a>Afficher des données personnalisées sur une carte raster

Cet article explique comment utiliser un [service d’image statique](https://docs.microsoft.com/rest/api/maps/render/getmapimage) avec la fonctionnalité de composition d’image, afin de permettre les superpositions sur une carte raster. La composition d’image inclut la possibilité d’obtenir une vignette raster dotée de données supplémentaires, telles que des repères personnalisés, des étiquettes et des superpositions géométriques. Pour afficher une géométrie, des étiquettes et des repères personnalisés, nous allons utiliser l’application Postman. Ouvrez l’application Postman, cliquez sur New (Nouveau) | Create New (Créer), sélectionnez et nommez une collection ou un dossier d’enregistrement et cliquez sur Save (Enregistrer).

Nous allons utiliser les [API du service de données](https://docs.microsoft.com/rest/api/maps/data) d’Azure Maps pour stocker et afficher les superpositions. 


## <a name="prerequisites"></a>Prérequis

### <a name="create-an-azure-maps-account"></a>Créer un compte Azure Maps 

Pour suivre les étapes de ce guide, vous devez tout d’abord lire l’article [Gérer le compte et les clés](how-to-manage-account-keys.md) afin de créer et de gérer votre abonnement de compte au tarif S1.

## <a name="render-pushpins-with-labels-and-custom-image"></a>Afficher des repères avec des étiquettes et une image personnalisée

> [!Note]
> Cet exemple nécessite un compte Azure Maps au tarif S0 ou S1. 

La référence (SKU) S0 du compte Azure Maps ne prend en charge qu’une seule instance du paramètre `pins` permettant aux utilisateurs d’afficher jusqu’à 5 repères spécifiés dans la requête d’url avec une image personnalisée.

Pour afficher des repères avec des étiquettes et une image personnalisée, suivez les étapes ci-dessous :

1. Ouvrez l’application Postman, cliquez sur New (Nouveau) | Create New (Créer), puis sélectionnez Request (Requête). Entrez un nom de requête pour afficher les repères, sélectionnez la collection ou le dossier dans lequel l’enregistrer, puis cliquez sur Save (Enregistrer).
    
    ![Chargement de limites géographiques à l’aide de Postman](./media/tutorial-geofence/postman-new.png)

2. Sélectionnez la méthode HTTP GET sous l’onglet Builder (Générateur), puis entrez l’URL suivante pour envoyer une requête GET.

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.98,%2040.77&pins=custom%7Cla15+50%7Cls12%7Clc003b61%7C%7C%27CentralPark%27-73.9657974+40.781971%7C%7Chttp%3A%2F%2Fazuremapscodesamples.azurewebsites.net%2FCommon%2Fimages%2Fpushpins%2Fylw-pushpin.png
    ```
    L’image suivante est la réponse que vous obtenez.

    ![Affichage de repères personnalisés avec des étiquettes](./media/how-to-render-custom-data/render-pins.png)


## <a name="get-data-from-azure-maps-data-storage"></a>Obtenir des données à partir du stockage de données Azure Maps

> [!Note]
> Cet exemple nécessite un compte Azure Maps au tarif S1.

Le chemin et les informations d’emplacement des repères peuvent être également obtenus par l’intermédiaire de l’[API de chargement des données](https://docs.microsoft.com/rest/api/maps/mapdata/upload). Suivez les étapes ci-dessous pour charger les données du chemin et des repères.

1. Dans l’application Postman, ouvrez un nouvel onglet dans la collection que vous avez créée précédemment. Sélectionnez la méthode HTTP POST sous l’onglet Builder (Générateur), puis entrez l’URL suivante pour envoyer une requête POST :

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

2. Cliquez sur Params, puis entrez les paires clé/valeur suivantes à utiliser pour l’URL de la requête POST. Remplacez la valeur subscription-key par votre clé d’abonnement Azure Maps.
    
    ![Paramètres clé/valeur dans Postman](./media/how-to-render-custom-data/postman-key-vals.png)

3. Cliquez sur **Body** (Corps), sélectionnez le format d’entrée Raw (Brut), puis choisissez JSON comme format d’entrée dans la liste déroulante. Fournissez le code JSON suivant en tant que données à charger :
    
    ```JSON
    {
      "type": "FeatureCollection",
      "features": [
        {
          "type": "Feature",
          "properties": {},
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -73.98235,
                  40.76799
                ],
                [
                  -73.95785,
                  40.80044
                ],
                [
                  -73.94928,
                  40.7968
                ],
                [
                  -73.97317,
                  40.76437
                ],
                [
                  -73.98235,
                  40.76799
                ]
              ]
            ]
          }
        },
        {
          "type": "Feature",
          "properties": {},
          "geometry": {
            "type": "LineString",
            "coordinates": [
              [
                -73.97624731063843,
                40.76560773817073
              ],
              [
                -73.97914409637451,
                40.766826609362575
              ],
              [
                -73.98513078689575,
                40.7585866048861
              ]
            ]
          }
        }
      ]
    }
    ```

4. Cliquez sur Send (Envoyer), puis examinez l’en-tête de la réponse. L’en-tête d’emplacement contient l’URI permettant d’accéder aux données ou de les télécharger pour une utilisation ultérieure. Il contient également la valeur unique `udId` pour les données chargées.   

  ```HTTP
  https://atlas.microsoft.com/mapData/{udId}/status?api-version=1.0&subscription-key={Subscription-key}
  ```



5. Utilisez la valeur de `udid` reçue de l’API de chargement des données pour afficher les fonctions sur la carte. Pour ce faire, ouvrez un nouvel onglet dans la collection que vous avez créée précédemment. Sélectionnez la méthode HTTP GET sous l’onglet Builder (Générateur), puis entrez l’URL suivante pour envoyer une requête GET :

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.96682739257812%2C40.78119135317995&pins=default|la-35+50|ls12|lc003C62|co9B2F15||'Times Square'-73.98516297340393 40.758781646381024|'Central Park'-73.96682739257812 40.78119135317995&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.30||udid-{udId}
    ```

6. L’image de réponse doit ressembler à celle-ci :

    ![Affichage des données chargées](./media/how-to-render-custom-data/uploaded-path.png)

## <a name="render-polygon-with-color-and-opacity"></a>Afficher un polygone avec une couleur et une opacité

> [!Note]
> Cet exemple nécessite un compte Azure Maps au tarif S1.

Vous pouvez modifier l’apparence d’un polygone en utilisant des modificateurs de style avec le [paramètre path](https://docs.microsoft.com/rest-staging/api/maps/render/getmapimage#uri-parameters).

1. Dans l’application Postman, ouvrez un nouvel onglet dans la collection que vous avez créée précédemment. Sélectionnez la méthode HTTP GET sous l’onglet Builder (Générateur), puis entrez l’URL suivante pour envoyer une requête GET afin d’afficher un polygone de couleur opaque :
    
    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&sku=S1&zoom=14&height=500&Width=500&center=-74.040701, 40.698666&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.50||-74.03995513916016 40.70090237454063|-74.04082417488098 40.70028420372218|-74.04113531112671 40.70049568385827|-74.04298067092896 40.69899904076542|-74.04271245002747 40.69879568992435|-74.04367804527283 40.6980961582905|-74.04364585876465 40.698055487620714|-74.04368877410889 40.698022951066996|-74.04168248176573 40.696444909137|-74.03901100158691 40.69837271818651|-74.03824925422668 40.69837271818651|-74.03809905052185 40.69903971085914|-74.03771281242369 40.699340668780984|-74.03940796852112 40.70058515602143|-74.03948307037354 40.70052821920425|-74.03995513916016 40.70090237454063
    &subscription-key={subscription--key}
    ```

L’image de réponse doit ressembler à celle-ci :

![Affichage d’un polygone opaque](./media/how-to-render-custom-data/opaque-polygon.png)


## <a name="render-polygon-with-circle-and-push-pins-with-custom-labels"></a>Afficher un polygone en forme de cercle contenant des repères dotés d’étiquettes personnalisées

> [!Note]
> Cet exemple nécessite un compte Azure Maps au tarif S1.

Vous pouvez augmenter ou diminuer la taille des repères, ainsi que celle de leurs étiquettes à l’aide du modificateur de style de l’échelle « sc ». Il s’agit d’une valeur supérieure à zéro. La valeur 1 représente l’échelle standard. Les valeurs supérieures à 1 agrandissent les repères tandis que les valeurs qui lui sont inférieures les diminuent. Pour plus d’informations sur les modificateurs de style, consultez [Paramètres du chemin du service d’image statique](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters).

Suivez les étapes ci-dessous pour afficher un polygone en forme de cercle doté de repères avec des étiquettes personnalisées :

1. Dans l’application Postman, ouvrez un nouvel onglet dans la collection que vous avez créée précédemment. Sélectionnez la méthode HTTP GET sous l’onglet Builder (Générateur), puis entrez l’URL suivante pour envoyer une requête GET :

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co002D62||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

L’image de réponse doit ressembler à celle-ci :

![Affichage d’un cercle avec des repères personnalisés](./media/how-to-render-custom-data/circle-custom-pins.png)

## <a name="next-steps"></a>Étapes suivantes

* Explorez la documentation de l’[API Azure Maps Get Map Image](https://docs.microsoft.com/rest/api/maps/search).
* Pour en savoir plus sur les fonctionnalités du service de données Azure Maps, consultez la [documentation du service](https://docs.microsoft.com/rest/api/maps/data).