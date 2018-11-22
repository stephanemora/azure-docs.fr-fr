---
title: Guide pratique pour ajouter des objets blobs à des objets dans Azure Digital Twins | Microsoft Docs
description: Comprendre comment ajouter des objets blob à des objets dans Azure Digital Twins
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: adgera
ms.openlocfilehash: 0929a4a63eee35d21723c980887d6b4217898682
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2018
ms.locfileid: "51688317"
---
# <a name="how-to-add-blobs-to-objects-in-azure-digital-twins"></a>Comment ajouter des objets blob à des objets dans Azure Digital Twins

Les objets blob sont des représentations non structurées de types de fichiers courants (tels que les images et les journaux). Les objets blob assurent le suivi du type de données qu’ils représentent à l’aide d’un type MIME (par exemple : « image/jpeg ») et de métadonnées (nom, description, type, etc.).

Azure Digital Twins prend en charge l’attachement d’objets blobs à des appareils, espaces et utilisateurs. Les objets blob peuvent représenter une image de profil pour un utilisateur, une photo de l’appareil, une vidéo, un mappage ou un journal.

> [!NOTE]
> Cet article suppose :
> * que votre instance est correctement configurée pour recevoir des demandes de l’API de gestion ;
> * que vous avez été correctement authentifié à l’aide d’un client REST de votre choix.

## <a name="uploading-blobs-an-overview"></a>Téléchargement d’objets blob : une vue d’ensemble

Des demandes en plusieurs parties sont utilisées pour charger des objets blob vers des points de terminaison spécifiques et leurs fonctionnalités respectives.

> [!IMPORTANT]
> Les demandes en plusieurs parties nécessitent trois éléments d’information essentiels. Pour Azure Digital Twins :
> * Un en-tête **Content-Type** :
>   * `application/json; charset=utf-8`
>   * `multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`.
> * Un texte **Content-Disposition**: `form-data; name="metadata"`.
> * Le contenu du fichier à charger.
>
> Le texte **Content-Type** et **Content-Disposition** exact peut varier en fonction du scénario d’utilisation.

Chaque demande en plusieurs parties est divisée en plusieurs blocs. Les demandes en plusieurs parties faites aux API de gestion d’Azure Digital Twins sont divisées en **deux** (**2**) composants de ce type :

1. La première partie est requise et contient des métadonnées d’objet blob comme un type MIME associé conformément au **Content-Type** et au **Content-Disposition** ci-dessus.

1. La deuxième partie contient le contenu d’objet blob réel (le contenu non structuré du fichier).  

Aucune des deux parties n’est obligatoire pour les demandes **PATCH**. Les deux sont requises pour **POST** ou pour créer des opérations.

### <a name="blob-metadata"></a>Métadonnées d'objet blob

En plus de **Content-Type** et de **Content-Disposition**, les demandes en plusieurs parties doivent également spécifier le corps JSON correct. Le corps JSON à soumettre varie selon le type d’opération de requête HTTP en cours d’exécution.

Les quatre principaux schémas JSON utilisés sont :

![Objets blob d’espace][1]

Ces schémas de modèle sont décrits en détail dans la documentation Swagger fournie.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

En savoir plus sur l’utilisation de la documentation de référence fournie en lisant [comment utiliser Swagger](./how-to-use-swagger.md).

### <a name="examples"></a>Exemples

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Pour effectuer une demande POST qui télécharge un fichier texte sous forme d’objet blob et l’associe à un espace :

```plaintext
POST YOUR_MANAGEMENT_API_URL/spaces/blobs HTTP/1.1
Content-Type: multipart/form-data; boundary="USER_DEFINED_BOUNDARY"

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

| Valeur du paramètre | Remplacer par |
| --- | --- |
| *USER_DEFINED_BOUNDARY* | Nom de la limite de contenu en plusieurs parties |

Une implémentation .NET du même chargement d’objet blob est fournie ci-dessous à l’aide de la classe [MultipartFormDataContent](https://docs.microsoft.com/dotnet/api/system.net.http.multipartformdatacontent) :

```csharp
//Supply your metaData in a suitable format
var multipartContent = new MultipartFormDataContent("USER_DEFINED_BOUNDARY");

var metadataContent = new StringContent(JsonConvert.SerializeObject(metaData), Encoding.UTF8, "application/json");
metadataContent.Headers.ContentType = MediaTypeHeaderValue.Parse("application/json; charset=utf-8");
multipartContent.Add(metadataContent, "metadata");

var fileContents = new StringContent("MY_BLOB.txt");
fileContents.Headers.ContentType = MediaTypeHeaderValue.Parse("text/plain");
multipartContent.Add(fileContents, "contents");

var response = await httpClient.PostAsync("spaces/blobs", multipartContent);
```

## <a name="api-endpoints"></a>Points de terminaison d’API

Ci-dessous, une procédure pas à pas de points de terminaison de base et de leurs fonctionnalités spécifiques est fournie.

### <a name="devices"></a>Appareils

Des objets blob peuvent être associés aux appareils. L'image ci-dessous (illustrant la documentation de référence Swagger pour vos API de gestion) spécifie les paramètres API liés à l’appareil pour la consommation blob et tous les paramètres de chemin d’accès requis pour y accéder :

![Objets blob d’appareil][2]

Par exemple, pour mettre à jour ou créer un objet blob et attacher l’objet blob à un appareil, une demande PATCH est faite pour :

```plaintext
YOUR_MANAGEMENT_API_URL/devices/blobs/YOUR_BLOB_ID
```

| Paramètre | Remplacer par |
| --- | --- |
| *YOUR_BLOB_ID* | L’ID d’objet blob requis |

Des demandes réussies retourneront un objet DeviceBlob JSON dans la réponse. DeviceBlobs conforme au schéma JSON suivant :

| Attribut | type | Description | Exemples |
| --- | --- | --- | --- |
| **DeviceBlobType** | Chaîne | Une catégorie d’objet blob qui peut être attachée à un appareil | `Model` et `Specification` |
| **DeviceBlobSubtype*** | Chaîne | Une sous-catégorie d’objet blob est plus précise que DeviceBlobType | `PhysicalModel`, `LogicalModel`, `KitSpecification` et `FunctionalSpecification` |

> [!TIP]
> Utilisez le tableau ci-dessus pour gérer les données de la demande retournées.

### <a name="spaces"></a>Espaces

Les objets blob peuvent également être attachés à des espaces. L’image ci-dessous répertorie tous les points de terminaison de l’API d’espace responsables du traitement des objets blob, ainsi que tous les paramètres de chemin d’accès à leur transmettre :

![Objets blob d’espace][3]

Par exemple, pour retourner un objet blob attaché à un espace, adressez une demande GET à :

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs/YOUR_BLOB_ID
```

| Paramètre | Remplacer par |
| --- | --- |
| *YOUR_BLOB_ID* | L’ID d’objet blob requis |

Faire une demande PATCH au même point de terminaison vous permettra de mettre à jour une description de métadonnées et de créer une nouvelle version de l’objet Blob. La requête HTTP est effectuée à l’aide de la méthode PATCH ainsi que des métadonnées nécessaires et des données de formulaire en plusieurs parties.

Des opérations réussies retourneront un SpaceBlob conforme au schéma suivant et peuvent être utilisées pour se servir des données retournées :

| Attribut | type | Description | Exemples |
| --- | --- | --- | --- |
| **SpaceBlobType** | Chaîne | Une catégorie d’objet blob qui peut être attachée à un espace | `Map` et `Image` |
| **SpaceBlobSubtype** | Chaîne | Une sous-catégorie d’objet blob est plus précise que SpaceBlobType | `GenericMap`, `ElectricalMap`, `SatelliteMap` et `WayfindingMap` |

### <a name="users"></a>Utilisateurs

Les objets blob peuvent être associés à des modèles de l’utilisateur (c’est-à dire associer une image de profil). L’image ci-dessous illustre les points de terminaison pertinents de l’API Utilisateurs et les paramètres de chemin d’accès requis, tels qu’un `id` :

![Objets blob d’utilisateur][4]

Par exemple, pour extraire un objet blob associé à un utilisateur, effectuez une demande GET avec les données de formulaire requises pour :

```plaintext
YOUR_MANAGEMENT_API_URL/users/blobs/YOUR_BLOB_ID
```

| Paramètre | Remplacer par |
| --- | --- |
| *YOUR_BLOB_ID* | L’ID d’objet blob requis |

Le JSON retourné (UserBlobs) est conforme aux modèles JSON suivants :

| Attribut | type | Description | Exemples |
| --- | --- | --- | --- |
| **UserBlobType** | Chaîne | Une catégorie d’objet blob qui peut être attachée à un utilisateur | `Image` et `Video` |
| **UserBlobSubtype** |  Chaîne | Une sous-catégorie d’objet blob est plus précise que UserBlobType | `ProfessionalImage`, `VacationImage` et `CommercialVideo` |

## <a name="common-errors"></a>Erreurs courantes

N’inclut pas les informations d’en-tête correctes :

```JSON
{
    "error": {
        "code": "400.600.000.000",
        "message": "Invalid media type in first section."
    }
}
```

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la documentation de référence Azure Digital Twins Swagger fournie, [Comment utiliser Digital Twins Swagger](how-to-use-swagger.md).

<!-- Images -->
[1]: media/how-to-add-blobs/blob-models.PNG
[2]: media/how-to-add-blobs/blobs-device-api.PNG
[3]: media/how-to-add-blobs/blobs-space-api.PNG
[4]: media/how-to-add-blobs/blobs-users-api.PNG
