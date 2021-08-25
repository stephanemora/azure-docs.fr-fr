---
title: 'Tutoriel : Utiliser Microsoft Azure Maps Creator pour créer des cartes d’intérieur'
description: Tutoriel sur la façon d’utiliser Microsoft Azure Maps Creator pour créer des cartes d’intérieur
author: anastasia-ms
ms.author: v-stharr
ms.date: 5/19/2021
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: 7f3fbec3fd393bb354f7ea5df1614cf1c53bfbe2
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121733435"
---
# <a name="tutorial-use-creator-to-create-indoor-maps"></a>Tutoriel : Utiliser le Créateur pour créer des cartes d’intérieur

Ce tutoriel montre comment créer des cartes d’intérieur. Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Charger votre package de dessin de carte d’intérieur
> * Convertir votre package de dessin en données cartographiques
> * Créer un jeu de données à partir de vos données cartographiques
> * Créer un tileset à partir des données de votre jeu de données
> * Interroger l’API WFS (Web Feature Service) Azure Maps pour découvrir les caractéristiques de votre carte
> * Créer un stateset de caractéristique à l’aide des caractéristiques de votre carte et des données de votre jeu de données
> * Mettre à jour votre stateset de caractéristique

## <a name="prerequisites"></a>Prérequis

1. [Créer un compte Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account).
2. [Obtenir une clé d’abonnement principale](quick-demo-map-app.md#get-the-primary-key-for-your-account), également appelée clé primaire ou clé d’abonnement.
3. [Créer une ressource de Créateur](how-to-manage-creator.md).
4. Téléchargez l’[exemple de package de dessin](https://github.com/Azure-Samples/am-creator-indoor-data-examples/blob/master/Sample%20-%20Contoso%20Drawing%20Package.zip).

Ce tutoriel utilise l’application [Postman](https://www.postman.com/), mais vous pouvez utiliser un autre environnement de développement d’API.

>[!IMPORTANT]
> Ce tutoriel utilise l’URL géographique `us.atlas.microsoft.com`. Si votre service Creator n’a pas été créé aux États-Unis, vous devez utiliser une autre URL géographique.  Pour plus d’informations, consultez [Accès aux services Creator](how-to-manage-creator.md#access-to-creator-services). Pour afficher les mappages de la région à l’emplacement géographique, [consultez Limite géographique du service Creator](creator-geographic-scope.md).

## <a name="upload-a-drawing-package"></a>Charger un package de dessin

Utilisez l’[API de chargement de données](/rest/api/maps/data-v2/upload-preview) pour charger le package de dessin dans les ressources Azure Maps.

L’API de chargement de données est une transaction de longue durée qui implémente le modèle défini dans [API d’opération durable de Creator V2](creator-long-running-operation-v2.md).

Pour charger le package de dessin :

1. Dans l’application Postman, sélectionnez **New** (Nouveau).

2. Dans la fenêtre **Create New** (Créer), sélectionnez **HTTP Request** (Requête HTTP).

3. Entrez un **Request name** (Nom de demande) pour la demande, par exemple *POST Data Upload*.

4. Sélectionnez la méthode HTTP **POST**.

5. Entrez l’URL suivante pour l’[API de chargement des données](/rest/api/maps/data-v2/upload-preview). La demande doit ressembler à l’URL suivante (remplacez `{Azure-Maps-Primary-Subscription-key}` par votre clé d’abonnement principale) :

    ```http
    https://us.atlas.microsoft.com/mapData?api-version=2.0&dataFormat=dwgzippackage&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

6. Sélectionnez l’onglet **En-têtes**.

7. Dans le champ **KEY** (CLÉ), sélectionnez `Content-Type`. 

8. Dans le champ **VALUE** (VALEUR), sélectionnez `application/octet-stream`.

     :::image type="content" source="./media/tutorial-creator-indoor-maps/data-upload-header.png"alt-text="Informations de l’onglet des en-têtes pour le chargement des données.":::

9. Sélectionnez l’onglet **Corps** .

10. Dans la liste déroulante, sélectionnez **binary** (binaire).

11. Sélectionnez **Select File** (Sélectionner un fichier), puis sélectionnez un package de dessin.

    :::image type="content" source="./media/tutorial-creator-indoor-maps/data-upload-body.png" alt-text="Sélectionnez un package de dessin.":::

12. Sélectionnez **Envoyer**.

13. Dans la fenêtre de réponse, sélectionnez l’onglet **En-têtes**.

14. Copiez la valeur de la clé **Operation-Location**, qui correspond à `status URL`. Nous allons utiliser `status URL` pour vérifier l’état du chargement du package de dessin.

     :::image type="content" source="./media/tutorial-creator-indoor-maps/data-upload-response-header.png" alt-text="Copiez l’URL d’état dans la clé Location.":::

### <a name="check-the-drawing-package-upload-status"></a>Vérifier l’état du chargement du package de dessin

Pour vérifier l’état du package de dessin et récupérer son ID unique (`udid`) :

1. Dans l’application Postman, sélectionnez **New** (Nouveau).

2. Dans la fenêtre **Créer**, sélectionnez **Requête HTTP**.

3. Entrez un **Nom de requête** pour la requête, par exemple *État chargement données GET*.

4. Sélectionnez la méthode HTTP **GET**.

5. Entrez le `status URL` que vous avez copié dans [Charger un package de dessin](#upload-a-drawing-package). La demande doit ressembler à l’URL suivante (remplacez `{Azure-Maps-Primary-Subscription-key}` par votre clé d’abonnement principale) :

    ```http
    https://us.atlas.microsoft.com/mapData/operations/<operationId>?api-version=2.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

6. Sélectionnez **Envoyer**.

7. Dans la fenêtre de réponse, sélectionnez l’onglet **En-têtes**.

8. Copiez la valeur de la clé **Resource-Location**, qui correspond à `resource location URL`. `resource location URL` contient l’identificateur unique (`udid`) de la ressource de package de dessin.

    :::image type="content" source="./media/tutorial-creator-indoor-maps/resource-location-url.png" alt-text="Copiez l’URL de l’emplacement de la ressource.":::

### <a name="optional-retrieve-drawing-package-metadata"></a>(Facultatif) Récupérer les métadonnées du package de dessin

Vous pouvez récupérer des métadonnées à partir de la ressource de package de dessin. Les métadonnées contiennent des informations telles que l’URL de la localisation de la ressource, la date de création, la date de mise à jour, la taille et l’état du chargement.

Pour récupérer les métadonnées de contenu :

1. Dans l’application Postman, sélectionnez **New** (Nouveau).

2. Dans la fenêtre **Créer**, sélectionnez **Requête HTTP**.

3. Entrez un **Request name** (Nom de requête) pour la requête, par exemple *GET Data Upload Metadata*.

4. . Sélectionnez la méthode HTTP **GET**.

5. Entrez le `resource Location URL` que vous avez copié dans [Vérifier l’état du chargement du package de dessin](#check-the-drawing-package-upload-status). La requête doit ressembler à l’URL suivante (remplacez `{Azure-Maps-Primary-Subscription-key}` par votre clé d’abonnement principale) :

    ```http
   https://us.atlas.microsoft.com/mapData/metadata/{udid}?api-version=2.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

6. Sélectionnez **Envoyer**.

7. Dans la fenêtre de réponse, sélectionnez l’onglet **Body** (Corps). Les métadonnées doivent ressembler au fragment JSON suivant :

    ```json
    {
        "udid": "{udid}",
        "location": "https://us.atlas.microsoft.com/mapData/6ebf1ae1-2a66-760b-e28c-b9381fcff335?api-version=2.0",
        "created": "5/18/2021 8:10:32 PM +00:00",
        "updated": "5/18/2021 8:10:37 PM +00:00",
        "sizeInBytes": 946901,
        "uploadStatus": "Completed"
    }
    ```

## <a name="convert-a-drawing-package"></a>Convertir un package de dessin

Le package de dessin étant chargé, nous allons utiliser l’`udid` pour le convertir en données cartographiques. L’API de conversion utilise une transaction de longue durée qui implémente le modèle défini [ici](creator-long-running-operation-v2.md).

Pour convertir un package de dessin :

1. Dans l’application Postman, sélectionnez **New** (Nouveau).

2. Dans la fenêtre **Create New** (Créer), sélectionnez **HTTP Request** (Requête HTTP).

3. Entrez un **Request name** (Nom de requête) pour la requête, par exemple *POST Convert Drawing Package*.

4. Sélectionnez la méthode HTTP **POST**.

5. Entrez l’URL suivante pour le [service de conversion](/rest/api/maps/v2/conversion/convert) (remplacez `{Azure-Maps-Primary-Subscription-key}` par votre clé d’abonnement principale et `udid` par l’`udid` du package chargé) :

    ```http
    https://us.atlas.microsoft.com/conversions?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=2.0&udid={udid}&inputType=DWG&outputOntology=facility-2.0
    ```

6. Sélectionnez **Envoyer**.

7. Dans la fenêtre de réponse, sélectionnez l’onglet **En-têtes**. 

8. Copiez la valeur de la clé **Operation-Location**, qui correspond à `status URL`. Nous allons utiliser `status URL` pour vérifier l’état de la conversion.

    :::image type="content" source="./media/tutorial-creator-indoor-maps/data-convert-location-url.png" border="true" alt-text="Copiez la valeur de la clé de la localisation pour le package de dessin.":::

### <a name="check-the-drawing-package-conversion-status"></a>Vérifier l’état de la conversion du package de dessin

Une fois l’opération de conversion terminée, elle retourne un `conversionId`. Nous pouvons accéder au `conversionId` en vérifiant l’état du processus de conversion du package de dessin. Le `conversionId` peut ensuite être utilisé pour accéder aux données converties.

Pour vérifier l’état du processus de conversion et récupérer le `conversionId` :

1. Dans l’application Postman, sélectionnez **New** (Nouveau).

2. Dans la fenêtre **Create New** (Créer), sélectionnez **HTTP Request** (Requête HTTP).

3. Entrez un **Request name** (Nom de requête) pour la requête, par exemple *GET Conversion Status*.

4. Sélectionnez la méthode HTTP **GET** :

5. Entrez le `status URL` que vous avez copié dans [Convertir un package de dessin](#convert-a-drawing-package). La requête doit ressembler à l’URL suivante (remplacez `{Azure-Maps-Primary-Subscription-key}` par votre clé d’abonnement principale) :

    ```http
    https://us.atlas.microsoft.com/conversions/operations/<operationId>?api-version=2.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

6. Sélectionnez **Envoyer**.

7. Dans la fenêtre de réponse, sélectionnez l’onglet **En-têtes**. 

8. Copiez la valeur de la clé **Resource-Location**, qui correspond à `resource location URL`. `resource location URL` contient l’identificateur unique (`conversionId`), qui peut être utilisé par d’autres API pour accéder aux données de carte converties.

      :::image type="content" source="./media/tutorial-creator-indoor-maps/data-conversion-id.png" alt-text="Copiez l’ID de conversion.":::

L’exemple de package de dessin doit être converti sans erreur ou avertissement. Toutefois, si vous recevez des erreurs ou des avertissements de votre propre package de dessin, la réponse JSON inclut un lien pointant vers le [visualiseur d’erreur de dessin](drawing-error-visualizer.md). Vous pouvez utiliser le visualiseur d’erreur de dessin pour examiner les détails des erreurs et avertissements. Pour recevoir des recommandations sur la résolution des erreurs et avertissements de conversion, consultez [Erreurs et avertissements liés à la conversion de dessin](drawing-conversion-error-codes.md).

Le fragment JSON suivant affiche un exemple d’avertissement de conversion :

```json
{
    "operationId": "<operationId>",
    "created": "2021-05-19T18:24:28.7922905+00:00",
    "status": "Succeeded",
     "warning": {
        "code": "dwgConversionProblem",
        "details": [
            {
                "code": "warning",
                "details": [
                    {
                        "code": "manifestWarning",
                        "message": "Ignoring unexpected JSON property: unitProperties[0].nonWheelchairAccessible with value False"
                    }
                ]
            }
        ]
    },
    "properties": {
        "diagnosticPackageLocation": "https://atlas.microsoft.com/mapData/ce61c3c1-faa8-75b7-349f-d863f6523748?api-version=1.0"
    }
}
```

## <a name="create-a-dataset"></a>Créer un jeu de données

Un jeu de données est une collection de caractéristiques cartographiques, telles que des bâtiments, des niveaux et des salles. Pour créer un jeu de données, utilisez l’[API de création de jeu de données](/rest/api/maps/v2/dataset/create). L’API de création jeu de données prend la valeur `conversionId` pour le package de dessin converti et retourne une valeur `datasetId` du jeu de données créé.

Pour créer un jeu de données :

1. Dans l’application Postman, sélectionnez **New** (Nouveau).

2. Dans la fenêtre **Create New** (Créer), sélectionnez **HTTP Request** (Requête HTTP).

3. Entrez un **Request name** (Nom de requête) pour la requête, par exemple *POST Dataset Create*.

4. Sélectionnez la méthode HTTP **POST**.

5. Entrez l’URL suivante pour l’[API de jeu de données](/rest/api/maps/v2/dataset). La requête doit ressembler à l’URL suivante (remplacez `{Azure-Maps-Primary-Subscription-key}` par votre clé d’abonnement principale) et `{conversionId`} par le `conversionId` obtenu dans [Vérifier l’état de la conversion du package de dessin](#check-the-drawing-package-conversion-status)) :

    ```http
    https://us.atlas.microsoft.com/datasets?api-version=2.0&conversionId={conversionId}&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

6. Sélectionnez **Envoyer**.

7. Dans la fenêtre de réponse, sélectionnez l’onglet **En-têtes**. 

8. Copiez la valeur de la clé **Operation-Location**, qui correspond à `status URL`. Nous allons utiliser `status URL` pour vérifier l’état du jeu de données.

    :::image type="content" source="./media/tutorial-creator-indoor-maps/data-dataset-location-url.png" border="true" alt-text="Copiez la valeur de la clé de la localisation pour le jeu de données.":::

### <a name="check-the-dataset-creation-status"></a>Vérifier l’état de la création du jeu de données

Pour vérifier l’état du processus de création du jeu de données et récupérer le `datasetId` :

1. Dans l’application Postman, sélectionnez **New** (Nouveau).

2. Dans la fenêtre **Create New** (Créer), sélectionnez **HTTP Request** (Requête HTTP).

3. Entrez un **Request name** (Nom de requête) pour la requête, par exemple *GET Dataset Status*.

4. Sélectionnez la méthode HTTP **GET**.

5. Entrez le `status URL` que vous avez copié dans [Créer un jeu de données](#create-a-dataset). La requête doit ressembler à l’URL suivante (remplacez `{Azure-Maps-Primary-Subscription-key}` par votre clé d’abonnement principale) :

    ```http
    https://us.atlas.microsoft.com/datasets/operations/<operationId>?api-version=2.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

6. Sélectionnez **Envoyer**.

7. Dans la fenêtre de réponse, sélectionnez l’onglet **Headers** (En-têtes). La valeur de la clé **Resource-Location** correspond à `resource location URL`. `resource location URL` contient l’identificateur unique (`datasetId`) du jeu de données. 

8. Copiez le `datasetId`, car vous allez l’utiliser dans les sections suivantes de ce tutoriel.

    :::image type="content" source="./media/tutorial-creator-indoor-maps/dataset-id.png" alt-text="Copiez l’ID du jeu de données.":::

## <a name="create-a-tileset"></a>Créer un tileset

Un tileset est un ensemble de vignettes vectorielles qui s’affichent sur la carte. Des tilesets sont créés à partir de jeux de données existants. Toutefois, un tileset est indépendant du jeu de données source. Si le jeu de données est supprimé, le tileset continue d’exister.

Pour créer un tileset :

1. Dans l’application Postman, sélectionnez **New** (Nouveau).

2. Dans la fenêtre **Create New** (Créer), sélectionnez **HTTP Request** (Requête HTTP).

3. Entrez un **Request name** (Nom de requête) pour la requête, par exemple *POST Tileset Create*.

4. Sélectionnez la méthode HTTP **POST**.

5. Entrez l’URL suivante pour l’[API de tileset](/rest/api/maps/v2/tileset). La requête doit ressembler à l’URL suivante (remplacez `{Azure-Maps-Primary-Subscription-key}` par votre clé d’abonnement principale) et `{datasetId`} par le `datasetId` obtenu dans [Vérifier l’état de la création du jeu de données](#check-the-dataset-creation-status)) :

    ```http
    https://us.atlas.microsoft.com/tilesets?api-version=2.0&datasetID={datasetId}&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

6. Sélectionnez **Envoyer**.

7. Dans la fenêtre de réponse, sélectionnez l’onglet **En-têtes**. 

8. Copiez la valeur de la clé **Operation-Location**, qui correspond à `status URL`. Nous allons utiliser `status URL` pour vérifier l’état du tileset.

    :::image type="content" source="./media/tutorial-creator-indoor-maps/data-tileset-location-url.png" border="true" alt-text="Copiez la valeur de l’URL de l’état du tileset.":::

### <a name="check-the-tileset-creation-status"></a>Vérifier l’état de la création du tileset

Pour vérifier l’état du processus de création du jeu de données et récupérer le `tilesetId` :

1. Dans l’application Postman, sélectionnez **New** (Nouveau).

2. Dans la fenêtre **Create New** (Créer), sélectionnez **HTTP Request** (Requête HTTP).

3. Entrez un **Request name** (Nom de requête) pour la requête, par exemple *GET Tileset Status*.

4. Sélectionnez la méthode HTTP **GET**.

5. Entrez le `status URL` que vous avez copié dans [Créer un tileset](#create-a-tileset). La requête doit ressembler à l’URL suivante (remplacez `{Azure-Maps-Primary-Subscription-key}` par votre clé d’abonnement principale) :

    ```http
    https://us.atlas.microsoft.com/tilesets/operations/<operationId>?api-version=2.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

6. Sélectionnez **Envoyer**.

7. Dans la fenêtre de réponse, sélectionnez l’onglet **Headers** (En-têtes). La valeur de la clé **Resource-Location** correspond à `resource location URL`.  `resource location URL` contient l’identificateur unique (`tilesetId`) du jeu de données.

    :::image type="content" source="./media/tutorial-creator-indoor-maps/tileset-id.png" alt-text="Copiez l’ID du tileset.":::

## <a name="query-datasets-with-wfs-api"></a>Interroger des jeux de données avec l’API de service de caractéristique web

Des jeux de données peuvent être interrogés à l’aide de l’[API WFS](/rest/api/maps/v2/wfs). Vous pouvez utiliser l’API WFS pour interroger toutes les collections de caractéristiques ou une collection spécifique. Dans cette section du tutoriel, nous ferons les deux opérations. Nous interrogerons d’abord toutes les collections, puis la collection `unit`.

### <a name="query-for-feature-collections"></a>Requête pour les collections de caractéristiques

Pour interroger toutes les collections de votre jeu de données :

1. Dans l’application Postman, sélectionnez **New** (Nouveau).

2. Dans la fenêtre **Create New** (Créer), sélectionnez **HTTP Request** (Requête HTTP).

3. Entrez un **Request name** (Nom de requête) pour la requête, par exemple *GET Dataset Collections*.

4. Sélectionnez la méthode HTTP **GET**.

5. Entrez l’URL suivante pour l’[API WFS](/rest/api/maps/v2/wfs). La requête doit ressembler à l’URL suivante (remplacez `{Azure-Maps-Primary-Subscription-key}` par votre clé d’abonnement principale) et `{datasetId`} par le `datasetId` obtenu dans [Vérifier l’état de la création du jeu de données](#check-the-dataset-creation-status)) :

    ```http
    https://us.atlas.microsoft.com/wfs/datasets/{datasetId}/collections?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=2.0
    ```

6. Sélectionnez **Envoyer**.

7. Le corps de la réponse est retourné au format GeoJSON et contient toutes les collections dans le jeu de données. Par souci de simplicité, l’exemple fourni ici affiche uniquement la collection `unit`. Pour voir un exemple contenant tous les collections, consultez [API Describe Collections du service de caractéristique web](/rest/api/maps/v2/wfs/collection-description). Pour en savoir plus sur une collection, vous pouvez sélectionner l’une des URL à l’intérieur de l’élément `link`.

    ```json
    {
    "collections": [
        {
            "name": "unit",
            "description": "A physical and non-overlapping area which might be occupied and traversed by a navigating agent. Can be a hallway, a room, a courtyard, etc. It is surrounded by physical obstruction (wall), unless the is_open_area attribute is equal to true, and one must add openings where the obstruction shouldn't be there. If is_open_area attribute is equal to true, all the sides are assumed open to the surroundings and walls are to be added where needed. Walls for open areas are represented as a line_element or area_element with is_obstruction equal to true.",
            "links": [
                {
                    "href": "https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit/definition?api-version=1.0",
                    "rel": "describedBy",
                    "title": "Metadata catalogue for unit"
                },
                {
                    "href": "https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit/items?api-version=1.0",
                    "rel": "data",
                    "title": "unit"
                }
                {
                    "href": "https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit?api-version=1.0",
                    "rel": "self",
                    "title": "Metadata catalogue for unit"
                }
            ]
        },
    ```

### <a name="query-for-unit-feature-collection"></a>Interroger la collection de caractéristiques unit

Dans cette section, nous allons interroger l’[API WFS](/rest/api/maps/v2/wfs) pour la collection de caractéristiques `unit`.

Pour interroger la collection unit dans votre jeu de données :

1. Dans l’application Postman, sélectionnez **New** (Nouveau).

2. Dans la fenêtre **Create New** (Créer), sélectionnez **HTTP Request** (Requête HTTP).

3. Entrez un **Request name** (Nom de requête) pour la requête, par exemple *GET Unit Collection*.

4. Sélectionnez la méthode HTTP **GET**.

5. Entrez l’URL suivante (remplacez `{Azure-Maps-Primary-Subscription-key}` par votre clé d’abonnement principale) et `{datasetId`} par le `datasetId` obtenu dans [Vérifier l’état de la création du jeu de données](#check-the-dataset-creation-status)) :

    ```http
    https://us.atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit/items?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=2.0
    ```

6. Sélectionnez **Envoyer**.

7. Une fois la réponse retournée, copiez l’`id` de l’une des caractéristiques `unit`. Dans l’exemple suivant, l’`id` de caractéristique est « UNIT26 ». Dans ce tutoriel, nous allons utiliser « UNIT26 » comme `id` de caractéristique dans la section suivante.

     ```json
    {
        "type": "FeatureCollection",
        "features": [
            {
                "type": "Feature",
                "geometry": {
                    "type": "Polygon",
                    "coordinates": ["..."]
                },
                "properties": {
                    "original_id": "b7410920-8cb0-490b-ab23-b489fd35aed0",
                    "category_id": "CTG8",
                    "is_open_area": true,
                    "navigable_by": [
                        "pedestrian"
                    ],
                    "route_through_behavior": "allowed",
                    "level_id": "LVL14",
                    "occupants": [],
                    "address_id": "DIR1",
                    "name": "157"
                },
                "id": "UNIT26",
                "featureType": ""
            }, {"..."}
        ]
    }
    ```

## <a name="create-a-feature-stateset"></a>Créer un stateset de caractéristique

Les statesets de caractéristique définissent des propriétés et des valeurs dynamiques sur des caractéristiques spécifiques qui les prennent en charge. Dans cette section, nous allons créer un stateset qui définit des valeurs booléennes et les styles correspondants pour la propriété **occupancy**.

Pour créer un stateset :

1. Dans l’application Postman, sélectionnez **New** (Nouveau).

2. Dans la fenêtre **Create New** (Créer), sélectionnez **HTTP Request** (Requête HTTP).

3. Entrez un **Request name** (Nom de requête) pour la requête, par exemple *POST Create Stateset*.

4. Sélectionnez la méthode HTTP **POST**.

5. Entrez l’URL suivante pour l’[API de stateset](/rest/api/maps/v2/feature-state/create-stateset). La requête doit ressembler à l’URL suivante (remplacez `{Azure-Maps-Primary-Subscription-key}` par votre clé d’abonnement principale) et `{datasetId`} par le `datasetId` obtenu dans [Vérifier l’état de la création du jeu de données](#check-the-dataset-creation-status)) :

    ```http
    https://us.atlas.microsoft.com/featurestatesets?api-version=2.0&datasetId={datasetId}&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

6. Sélectionnez l’onglet **En-têtes**.

7. Dans le champ **KEY** (CLÉ), sélectionnez `Content-Type`. 

8. Dans le champ **VALUE** (VALEUR), sélectionnez `application/json`.

     :::image type="content" source="./media/tutorial-creator-indoor-maps/stateset-header.png"alt-text="Informations sur l’onglet En-têtes pour la création de stateset.":::

9. Sélectionnez l’onglet **Corps** .

10. Dans les listes déroulantes, sélectionnez **raw** (brut) et **JSON**.

11. Copiez les styles JSON suivant, puis collez-les dans la fenêtre **Body** (Corps) :

    ```json
    {
       "styles":[
          {
             "keyname":"occupied",
             "type":"boolean",
             "rules":[
                {
                   "true":"#FF0000",
                   "false":"#00FF00"
                }
             ]
          }
       ]
    }
    ```

12. Sélectionnez **Envoyer**.

13. Une fois la réponse retournée, copiez le `statesetId` à partir du corps de la réponse. Dans la section suivante, nous allons utiliser le `statesetId` pour changer l’état de la propriété `occupancy` de l’unité avec l’`id` de caractéristique « UNIT26 ».

    :::image type="content" source="./media/tutorial-creator-indoor-maps/response-stateset-id.png"alt-text="Réponse de l’ID de stateset.":::

### <a name="update-a-feature-state"></a>Mettre à jour l’état d’une caractéristique

Pour mettre à jour l’état `occupied` de l’unité avec l’`id` de caractéristique « UNIT26 » :

1. Dans l’application Postman, sélectionnez **New** (Nouveau).

2. Dans la fenêtre **Create New** (Créer), sélectionnez **HTTP Request** (Requête HTTP).

3. Entrez un **Request name** (Nom de requête) pour la requête, par exemple *PUT Set Stateset*.

4. Sélectionnez la méthode HTTP **PUT**.

5. Entrez l’URL suivante pour l’[API des statesets de caractéristique](/rest/api/maps/v2/feature-state/create-stateset). La requête doit ressembler à l’URL suivante (remplacez `{Azure-Maps-Primary-Subscription-key}` par votre clé d’abonnement principale) et `{statesetId`} par le `statesetId` obtenu dans [Créer un stateset de caractéristique](#create-a-feature-stateset)) :

    ```http
    https://us.atlas.microsoft.com/featurestatesets/{statesetId}/featureStates/UNIT26?api-version=2.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

6. Sélectionnez l’onglet **En-têtes**.

7. Dans le champ **KEY** (CLÉ), sélectionnez `Content-Type`. 

8. Dans le champ **VALUE** (VALEUR), sélectionnez `application/json`.

     :::image type="content" source="./media/tutorial-creator-indoor-maps/stateset-header.png"alt-text="Informations sur l’onglet En-têtes pour la création de stateset.":::

9. Sélectionnez l’onglet **Corps** .

10. Dans les listes déroulantes, sélectionnez **raw** (brut) et **JSON**.

11. Copiez le style JSON suivant, puis collez-le dans la fenêtre **Body** (Corps) :

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

    >[!NOTE]
    > La mise à jour n’est enregistrée que si l’horodatage de la publication est postérieur à celui de la requête précédente.

12. Sélectionnez **Envoyer**.

13. Une fois la mise à jour terminée, vous recevez un code d’état HTTP `200 OK`. Si vous avez implémenté une [application de style dynamique](indoor-map-dynamic-styling.md) pour une carte d’intérieur, la mise à jour s’affiche à l’horodatage spécifié dans votre carte rendue.

Vous pouvez utiliser l’[API d’obtention de stateset de caractéristique](/rest/api/maps/v2/feature-state/get-states) pour récupérer l’état d’une caractéristique à l’aide de son `id` de caractéristique. Vous pouvez également utiliser l’[API de suppression d’état de caractéristique](/rest/api/maps/v2/feature-state/delete-stateset) pour supprimer le stateset et ses ressources.

Pour en savoir plus sur les différents services Azure Maps Creator abordés dans cet article, consultez [Creator pour cartes d’intérieur](creator-indoor-maps.md).

## <a name="clean-up-resources"></a>Nettoyer les ressources

Aucune ressource ne nécessite de nettoyage.

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment utiliser le module Indoor Maps, consultez

> [!div class="nextstepaction"]
> [Utiliser le module Cartes d’intérieur](how-to-use-indoor-module.md)
