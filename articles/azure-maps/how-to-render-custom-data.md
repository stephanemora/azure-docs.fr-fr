---
title: Comment afficher des données personnalisées sur une carte raster dans Azure Maps | Microsoft Docs
description: Effectuer le rendu des données personnalisées sur une carte raster dans Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 46f08aaa33563f620e7a011620730249e903f7b7
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58086596"
---
# <a name="render-custom-data-on-a-raster-map"></a>Afficher les données personnalisées sur une carte raster

Cet article explique comment utiliser le [service d’images statiques](https://docs.microsoft.com/rest/api/maps/render/getmapimage) avec la fonctionnalité de composition d’images pour permettre des superpositions sur une carte raster. Composition d’image inclut la capacité à obtenir une vignette raster, avec d’autres données telles que des punaises personnalisés, des étiquettes et des superpositions de géométrie.

Pour restituer des punaises personnalisés, des étiquettes et des superpositions de géométrie, vous pouvez utiliser l’application Postman. Vous pouvez utiliser Azure Maps [API de Service de données](https://docs.microsoft.com/rest/api/maps/data) pour stocker et afficher les superpositions.


## <a name="prerequisites"></a>Conditions préalables

### <a name="create-an-azure-maps-account"></a>Créer un compte Azure Maps

Pour effectuer les procédures décrites dans cet article, vous devez d’abord [créer un compte Azure Maps](how-to-manage-account-keys.md) dans le niveau tarifaire S1.

## <a name="render-pushpins-with-labels-and-a-custom-image"></a>Restituer des punaises avec des étiquettes et une image personnalisée

> [!Note]
> La procédure décrite dans cette section requiert un compte Azure Maps dans le niveau tarifaire S0 ou S1.

Les mappages Azure compte S0 pris en charge qu’une seule instance de la `pins` paramètre. Il vous pouvez ainsi afficher jusqu'à cinq punaises, spécifiés dans la demande d’URL, avec une image personnalisée.

Pour afficher les repères avec des étiquettes et une image personnalisée, procédez comme suit :

1. Créer un regroupement dans lequel stocker les demandes. Dans l’application Postman, sélectionnez **New**. Dans le **créer un nouveau** fenêtre, sélectionnez **Collection**. Nom de la collection et sélectionnez le **créer** bouton. 

2. Pour créer la demande, sélectionnez **New** à nouveau. Dans le **créer un nouveau** fenêtre, sélectionnez **demande**. Entrez un **nom de la demande** pour les repères, sélectionnez le regroupement que vous avez créé à l’étape précédente en tant que l’emplacement dans lequel enregistrer la demande, puis sélectionnez **enregistrer**.
    
    ![Créer une requête dans Postman](./media/tutorial-geofence/postman-new.png)

3. Sélectionnez la méthode HTTP GET sur l’onglet Générateur de rapports et entrez l’URL suivante pour créer une demande GET.

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.98,%2040.77&pins=custom%7Cla15+50%7Cls12%7Clc003b61%7C%7C%27CentralPark%27-73.9657974+40.781971%7C%7Chttp%3A%2F%2Fazuremapscodesamples.azurewebsites.net%2FCommon%2Fimages%2Fpushpins%2Fylw-pushpin.png
    ```
    Voici l’image résultante :

    ![Une punaise personnalisée avec une étiquette](./media/how-to-render-custom-data/render-pins.png)


## <a name="get-data-from-azure-maps-data-storage"></a>Obtenir des données à partir du stockage de données Azure Maps

> [!Note]
> La procédure décrite dans cette section requiert un compte Azure Maps dans le niveau tarifaire S1.

Vous pouvez également obtenir les informations d’emplacement de chemin d’accès et un code confidentiel à l’aide de la [API de charger des données](https://docs.microsoft.com/rest/api/maps/data/uploadpreview). Suivez les étapes ci-dessous pour charger les données du chemin et des repères.

1. Dans l’application Postman, ouvrez un nouvel onglet dans la collection que vous avez créé dans la section précédente. Sélectionnez la méthode HTTP POST sur l’onglet Générateur de rapports et entrez l’URL suivante pour effectuer une demande POST :

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

2. Sur le **Params** , entrez les paires clé/valeur suivantes, qui sont utilisés pour l’URL de la demande POST. Remplacez le `subscription-key` valeur avec votre clé d’abonnement Azure Maps.
    
    ![Paramètres de clé/valeur dans Postman](./media/how-to-render-custom-data/postman-key-vals.png)

3. Sur le **corps** , sélectionnez le format d’entrée brutes et choisissez JSON comme format d’entrée dans la liste déroulante. Fournissez ce JSON en tant que données à charger :
    
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

4. Sélectionnez **envoyer** et passez en revue l’en-tête de réponse. L’en-tête d’emplacement contient l’URI utilisé pour accéder ou de télécharger les données pour une utilisation ultérieure. Il contient également la valeur unique `udId` pour les données chargées.  

   ```HTTP
   https://atlas.microsoft.com/mapData/{udId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

5. Utilisez le `udId` valeur reçue à partir de l’API de charger des données à restituer sur la carte. Pour ce faire, ouvrez un nouvel onglet dans la collection que vous avez créé dans la section précédente. Sélectionnez la méthode HTTP GET sur l’onglet Générateur de rapports et entrer cette URL pour effectuer une demande GET :

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.96682739257812%2C40.78119135317995&pins=default|la-35+50|ls12|lc003C62|co9B2F15||'Times Square'-73.98516297340393 40.758781646381024|'Central Park'-73.96682739257812 40.78119135317995&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.30||udid-{udId}
    ```

6. Voici l’image résultante :

    ![Obtenir des données à partir du stockage de données Azure Maps](./media/how-to-render-custom-data/uploaded-path.png)

## <a name="render-a-polygon-with-color-and-opacity"></a>Rendu d’un polygone avec la couleur et l’opacité

> [!Note]
> La procédure décrite dans cette section requiert un compte Azure Maps dans le niveau tarifaire S1.


Vous pouvez modifier l’apparence d’un polygone en utilisant des modificateurs de style avec le [paramètre path](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters).

1. Dans l’application Postman, ouvrez un nouvel onglet dans la collection que vous avez créé précédemment. Sélectionnez la méthode HTTP GET sur l’onglet Générateur de rapports et entrez l’URL suivante pour configurer une demande GET pour restituer un polygone avec la couleur et l’opacité :
    
    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&sku=S1&zoom=14&height=500&Width=500&center=-74.040701, 40.698666&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.50||-74.03995513916016 40.70090237454063|-74.04082417488098 40.70028420372218|-74.04113531112671 40.70049568385827|-74.04298067092896 40.69899904076542|-74.04271245002747 40.69879568992435|-74.04367804527283 40.6980961582905|-74.04364585876465 40.698055487620714|-74.04368877410889 40.698022951066996|-74.04168248176573 40.696444909137|-74.03901100158691 40.69837271818651|-74.03824925422668 40.69837271818651|-74.03809905052185 40.69903971085914|-74.03771281242369 40.699340668780984|-74.03940796852112 40.70058515602143|-74.03948307037354 40.70052821920425|-74.03995513916016 40.70090237454063
    &subscription-key={subscription--key}
    ```

Voici l’image résultante :

![Dessiner un polygone opaque](./media/how-to-render-custom-data/opaque-polygon.png)


## <a name="render-a-circle-and-pushpins-with-custom-labels"></a>Afficher un cercle et punaises avec légendes personnalisées

> [!Note]
> La procédure décrite dans cette section requiert un compte Azure Maps dans le niveau tarifaire S1.


Vous pouvez rendre punaises et leurs étiquettes supérieure ou inférieure à l’aide de la `sc` modificateur de style de mise à l’échelle. Ce modificateur prend une valeur qui est supérieure à zéro. La valeur 1 représente l’échelle standard. Les valeurs supérieures à 1 agrandissent les repères tandis que les valeurs qui lui sont inférieures les diminuent. Pour plus d’informations sur les modificateurs de style, consultez [paramètres de chemin d’accès de service image statique](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters).


Suivez ces étapes pour rendre un cercle et punaises avec légendes personnalisées :

1. Dans l’application Postman, ouvrez un nouvel onglet dans la collection que vous avez créé précédemment. Sélectionnez la méthode HTTP GET sur l’onglet Générateur de rapports et entrer cette URL pour effectuer une demande GET :

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co002D62||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

Voici l’image résultante :

![Afficher un cercle avec des punaises personnalisés](./media/how-to-render-custom-data/circle-custom-pins.png)

## <a name="next-steps"></a>Étapes suivantes


* Explorez la documentation de l’[API Azure Maps Get Map Image](https://docs.microsoft.com/rest/api/maps/render/getmapimage).
* Pour en savoir plus sur le Service de données Azure Maps, consultez le [documentation service](https://docs.microsoft.com/rest/api/maps/data).

