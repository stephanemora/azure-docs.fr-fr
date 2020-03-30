---
title: Guide pratique pour ajouter des objets blob à des objets - Azure Digital Twins | Microsoft Docs
description: Découvrez comment ajouter des objets blob à des utilisateurs, appareils et espaces dans Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/10/2020
ms.custom: seodec18
ms.openlocfilehash: c85db05e6feeea43023c2391998f837348caed4e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75929618"
---
# <a name="add-blobs-to-objects-in-azure-digital-twins"></a>Ajouter des objets blob à des objets dans Azure Digital Twins

Les objets blob sont des représentations non structurées de types de fichier courants, comme les images et les journaux d’activité. Les blobs assurent le suivi du type de données qu’ils représentent à l’aide d’un type MIME (par exemple : « image/jpeg ») et de métadonnées (nom, description, type, etc.).

Azure Digital Twins prend en charge l’attachement d’objets blob à des appareils, des espaces et des utilisateurs. Les blobs peuvent représenter une image de profil pour un utilisateur, une photo de l’appareil, une vidéo, une carte, un zip de microprogramme, des données JSON, un journal, etc.

[!INCLUDE [Digital Twins Management API familiarity](../../includes/digital-twins-familiarity.md)]

## <a name="uploading-blobs-overview"></a>Vue d’ensemble du chargement d’objets blob

Vous pouvez utiliser des demandes en plusieurs parties pour charger des objets blob vers des points de terminaison spécifiques et leurs fonctionnalités respectives.

[!INCLUDE [Digital Twins multipart requests](../../includes/digital-twins-multipart.md)]

### <a name="blob-metadata"></a>Métadonnées d'objet blob

En plus de **Content-Type** et **Content-Disposition**, les demandes en plusieurs parties Azure Digital Twins doivent également spécifier le corps JSON approprié. Le corps JSON à envoyer varie selon le type d’opération de requête HTTP effectuée.

Les quatre principaux schémas JSON sont :

[![Schémas JSON](media/how-to-add-blobs/blob-models-swagger-img.png)](media/how-to-add-blobs/blob-models-swagger-img.png#lightbox)

Les métadonnées d’objets blob JSON sont conformes au modèle suivant :

```JSON
{
    "parentId": "00000000-0000-0000-0000-000000000000",
    "name": "My First Blob",
    "type": "Map",
    "subtype": "GenericMap",
    "description": "A well chosen description",
    "sharing": "None"
  }
```

| Attribut | Type | Description |
| --- | --- | --- |
| **parentId** | String | Entité parente avec laquelle associer l’objet blob (espaces, appareils ou utilisateurs) |
| **name** |String | Nom convivial pour l’objet blob |
| **type** | String | Type d’objet blob. Vous ne pouvez pas utiliser *type* et *typeId*  |
| **typeId** | Integer | ID de type d’objet blob. Vous ne pouvez pas utiliser *type* et *typeId* |
| **subtype** | String | Sous-type d’objet blob. Vous ne pouvez pas utiliser *subtype* et *subtypeId* |
| **subtypeId** | Integer | ID de sous-type d’objet blob. Vous ne pouvez pas utiliser *subtype* et *subtypeId* |
| **description** | String | Description personnalisée de l’objet blob |
| **sharing** | String | Indique si l’objet blob peut être partagé - enum [`None`, `Tree`, `Global`] |

Les métadonnées de blobs sont toujours fournies en tant que premier bloc avec **Content-Type** `application/json` ou en tant que fichier `.json`. Les données de fichiers sont fournies dans le deuxième segment et peuvent être de n’importe quel type MIME pris en charge.

La documentation Swagger décrit en détail ces schémas de modèle.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

Découvrez plus d’informations sur l’utilisation de la documentation de référence en lisant [Comment utiliser Swagger](./how-to-use-swagger.md).

### <a name="blobs-response-data"></a>Données de réponse d’objets blob

Les objets blob retournés individuellement sont conformes au schéma JSON suivant :

```JSON
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "parentId": "00000000-0000-0000-0000-000000000000",
  "type": "string",
  "subtype": "string",
  "typeId": 0,
  "subtypeId": 0,
  "sharing": "None",
  "description": "string",
  "contentInfos": [
    {
      "type": "string",
      "sizeBytes": 0,
      "mD5": "string",
      "version": "string",
      "lastModifiedUtc": "2019-01-12T00:58:08.689Z",
      "metadata": {
        "additionalProp1": "string",
        "additionalProp2": "string",
        "additionalProp3": "string"
      }
    }
  ],
  "fullName": "string",
  "spacePaths": [
    "string"
  ]
}
```

| Attribut | Type | Description |
| --- | --- | --- |
| **id** | String | Identificateur unique de l’objet blob |
| **name** |String | Nom convivial pour l’objet blob |
| **parentId** | String | Entité parente avec laquelle associer l’objet blob (espaces, appareils ou utilisateurs) |
| **type** | String | Type d’objet blob. Vous ne pouvez pas utiliser *type* et *typeId*  |
| **typeId** | Integer | ID de type d’objet blob. Vous ne pouvez pas utiliser *type* et *typeId* |
| **subtype** | String | Sous-type d’objet blob. Vous ne pouvez pas utiliser *subtype* et *subtypeId* |
| **subtypeId** | Integer | ID de sous-type d’objet blob. Vous ne pouvez pas utiliser *subtype* et *subtypeId* |
| **sharing** | String | Indique si l’objet blob peut être partagé - enum [`None`, `Tree`, `Global`] |
| **description** | String | Description personnalisée de l’objet blob |
| **contentInfos** | Array | Spécifie les informations de métadonnées non structurées, notamment la version |
| **fullName** | String | Nom complet de l’objet blob |
| **spacePaths** | String | Chemin de l’espace |

Les métadonnées de blobs sont toujours fournies en tant que premier bloc avec **Content-Type** `application/json` ou en tant que fichier `.json`. Les données de fichiers sont fournies dans le deuxième segment et peuvent être de n’importe quel type MIME pris en charge.

### <a name="blob-multipart-request-examples"></a>Exemples de requête multipart d’objet blob

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Pour charger un fichier texte sous forme de blob et l’associer à un espace, envoyez une requête HTTP POST authentifiée à :

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs
```

Avec le corps suivant :

```plaintext
--USER_DEFINED_BOUNDARY
Content-Type: application/json; charset=utf-8
Content-Disposition: form-data; name="metadata"

{
  "ParentId": "54213cf5-285f-e611-80c3-000d3a320e1e",
  "Name": "My First Blob",
  "Type": "Map",
  "SubType": "GenericMap",
  "Description": "A well chosen description",
  "Sharing": "None"
}
--USER_DEFINED_BOUNDARY
Content-Disposition: form-data; name="contents"; filename="myblob.txt"
Content-Type: text/plain

This is my blob content. In this case, some text, but I could also be uploading a picture, an JSON file, a firmware zip, etc.

--USER_DEFINED_BOUNDARY--
```

| Valeur | Remplacer par |
| --- | --- |
| USER_DEFINED_BOUNDARY | Nom de la limite de contenu en plusieurs parties |

Le code suivant est une implémentation .NET du même chargement d’objet blob mais en utilisant la classe [MultipartFormDataContent](https://docs.microsoft.com/dotnet/api/system.net.http.multipartformdatacontent) :

```csharp
//Supply your metadata in a suitable format
var multipartContent = new MultipartFormDataContent("USER_DEFINED_BOUNDARY");

var metadataContent = new StringContent(JsonConvert.SerializeObject(metaData), Encoding.UTF8, "application/json");
metadataContent.Headers.ContentType = MediaTypeHeaderValue.Parse("application/json; charset=utf-8");
multipartContent.Add(metadataContent, "metadata");

//MY_BLOB.txt is the String representation of your text file
var fileContents = new StringContent("MY_BLOB.txt");
fileContents.Headers.ContentType = MediaTypeHeaderValue.Parse("text/plain");
multipartContent.Add(fileContents, "contents");

var response = await httpClient.PostAsync("spaces/blobs", multipartContent);
```

Pour finir, les utilisateurs [cURL](https://curl.haxx.se/) peuvent effectuer des requêtes de formulaire multipart de la même manière :

```bash
curl -X POST "YOUR_MANAGEMENT_API_URL/spaces/blobs" \
 -H "Authorization: Bearer YOUR_TOKEN" \
 -H "Accept: application/json" \
 -H "Content-Type: multipart/form-data" \
 -F "meta={\"ParentId\":\"YOUR_SPACE_ID\",\"Name\":\"My CURL Blob\",\"Type\":\"Map\",\"SubType\":\"GenericMap\",\"Description\":\"A well chosen description\",\"Sharing\":\"None\"};type=application/json" \
 -F "text=PATH_TO_FILE;type=text/plain"
```

| Valeur | Remplacer par |
| --- | --- |
| YOUR_TOKEN | Votre jeton OAuth 2.0 valide |
| YOUR_SPACE_ID | ID de l’espace avec lequel associer l’objet blob |
| PATH_TO_FILE | Chemin de votre fichier texte |

[![Exemple cURL](media/how-to-add-blobs/http-blob-post-through-curl-img.png)](media/how-to-add-blobs/http-blob-post-through-curl-img.png#lightbox)

Une requête POST ayant réussi retourne l’ID du nouvel objet blob.

## <a name="api-endpoints"></a>Points de terminaison d’API

Les sections suivantes décrivent les points de terminaison d’API principaux liés à l’objet blob et leurs fonctionnalités.

### <a name="devices"></a>Appareils

Vous pouvez attacher des objets blob à des appareils. L’illustration suivante montre la documentation de référence Swagger pour vos API de gestion. Elle spécifie les points de terminaison d’API associés à l’appareil pour la consommation d’objets blob et tous les paramètres de chemin obligatoires à leur passer.

[![Objets blob d’appareil](media/how-to-add-blobs/blobs-device-api-swagger-img.png)](media/how-to-add-blobs/blobs-device-api-swagger-img.png#lightbox)

Par exemple, pour mettre à jour ou créer un blob, et l’attacher à un appareil, envoyez une requête HTTP PATCH authentifiée à :

```plaintext
YOUR_MANAGEMENT_API_URL/devices/blobs/YOUR_BLOB_ID
```

| Paramètre | Remplacer par |
| --- | --- |
| *YOUR_BLOB_ID* | ID d’objet blob souhaité |

Les requêtes ayant réussi retournent un objet JSON comme [décrit précédemment](#blobs-response-data).

### <a name="spaces"></a>Espaces

Vous pouvez également attacher des objets blob à des espaces. L’image suivante liste tous les points de terminaison d’API d’espace responsables du traitement des objets blob. Elle liste également tous les paramètres de chemin à passer à ces points de terminaison.

[![Objets blob d’espace](media/how-to-add-blobs/blobs-space-api-swagger-img.png)](media/how-to-add-blobs/blobs-space-api-swagger-img.png#lightbox)

Par exemple, pour retourner un blob attaché à un espace, envoyez une requête HTTP GET authentifiée à :

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs/YOUR_BLOB_ID
```

| Paramètre | Remplacer par |
| --- | --- |
| *YOUR_BLOB_ID* | ID d’objet blob souhaité |

Les requêtes ayant réussi retournent un objet JSON comme [décrit précédemment](#blobs-response-data).

Une requête PATCH au même point de terminaison met à jour la description des métadonnées et crée des versions de l’objet blob. La requête HTTP est envoyée à l’aide de la méthode PATCH ainsi que des métadonnées et des données de formulaire en plusieurs parties nécessaires.

### <a name="users"></a>Utilisateurs

Vous pouvez attacher des objets blob aux modèles utilisateur (par exemple, pour associer une image de profil). L’image suivante montre les points de terminaison pertinents de l’API utilisateur et les paramètres de chemin nécessaires, comme `id` :

[![Objets blob d’utilisateur](media/how-to-add-blobs/blobs-users-api-swagger-img.png)](media/how-to-add-blobs/blobs-users-api-swagger-img.png#lightbox)

Par exemple, pour extraire un blob attaché à un utilisateur, envoyez une requête HTTP GET authentifiée avec les données de formulaire nécessaires à :

```plaintext
YOUR_MANAGEMENT_API_URL/users/blobs/YOUR_BLOB_ID
```

| Paramètre | Remplacer par |
| --- | --- |
| *YOUR_BLOB_ID* | ID d’objet blob souhaité |

Les requêtes ayant réussi retournent un objet JSON comme [décrit précédemment](#blobs-response-data).

## <a name="common-errors"></a>Erreurs courantes

* Une erreur courante consiste à ne pas fournir les informations d’en-tête correctes :

  ```JSON
  {
      "error": {
          "code": "400.600.000.000",
          "message": "Invalid media type in first section."
      }
  }
  ```

  Pour résoudre cette erreur, vérifiez que la requête entière a un en-tête **Content-Type** approprié :

     * `multipart/mixed`
     * `multipart/form-data`

  Vérifiez également que chaque *bloc en plusieurs parties* a un **Content-Type** correspondant approprié.

* Une deuxième erreur courante survient lorsque plusieurs blobs sont assignés à la même ressource dans votre [graphique d’intelligence spatiale](concepts-objectmodel-spatialgraph.md) :

  ```JSON
  {
      "error": {
          "code": "400.600.000.000",
          "message": "SpaceBlobMetadata already exists."
      }
  }
  ```

  > [!NOTE]
  > L’attribut **message** varie en fonction de la ressource. 

  Un seul blob (de chaque type) peut être attaché à chaque ressource dans votre graphique spatial. 

  Pour résoudre cette erreur, mettez à jour le blob existant à l’aide de l’opération API HTTP PATCH appropriée. Cela remplacera les données blob existantes par les données souhaitées.

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur la documentation de référence Swagger pour Azure Digital Twins, lisez [Utiliser Azure Digital Twins Swagger](how-to-use-swagger.md).

- Pour découvrir comment charger des objets blob par le biais de Postman, consultez [Guide pratique pour configurer Postman](./how-to-configure-postman.md).