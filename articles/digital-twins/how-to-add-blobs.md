---
title: Guide pratique pour ajouter des objets blobs à des objets dans Azure Digital Twins | Microsoft Docs
description: Découvrez comment ajouter des objets blob à des objets dans Azure Digital Twins.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/28/2018
ms.author: adgera
ms.custom: seodec18
ms.openlocfilehash: 604093dcec048b0991bbc9beac3ef998cc47e351
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/02/2019
ms.locfileid: "53974509"
---
# <a name="add-blobs-to-objects-in-azure-digital-twins"></a>Ajouter des objets blob à des objets dans Azure Digital Twins

Les objets blob sont des représentations non structurées de types de fichier courants, comme les images et les journaux. Les objets blob assurent le suivi du type de données qu’ils représentent à l’aide d’un type MIME (par exemple : « image/jpeg ») et de métadonnées (nom, description, type, etc.).

Azure Digital Twins prend en charge l’attachement d’objets blob à des appareils, des espaces et des utilisateurs. Les objets blob peuvent représenter une image de profil pour un utilisateur, une photo de l’appareil, une vidéo, une carte ou un journal.

[!INCLUDE [Digital Twins Management API familiarity](../../includes/digital-twins-familiarity.md)]

## <a name="uploading-blobs-an-overview"></a>Téléchargement d’objets blob : une vue d’ensemble

Vous pouvez utiliser des demandes en plusieurs parties pour charger des objets blob vers des points de terminaison spécifiques et leurs fonctionnalités respectives.

> [!IMPORTANT]
> Les demandes en plusieurs parties nécessitent trois informations essentielles :
> * Un en-tête **Content-Type** :
>   * `application/json; charset=utf-8`
>   * `multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`
> * Un élément **Content-Disposition** : `form-data; name="metadata"`
> * Le contenu du fichier à charger
>
> Les informations **Content-Type** et **Content-Disposition** peuvent varier en fonction du scénario d’utilisation.

Les demandes en plusieurs parties faites aux API de gestion Azure Digital Twins comprennent deux parties :

* Des métadonnées blob, par exemple, un type MIME associé, comme indiqué dans les informations **Content-Type** et **Content-Disposition**

* Contenu blob (le contenu non structuré du fichier)  

Aucune des deux parties n’est obligatoire pour les demandes **PATCH**. Les deux sont requises pour **POST** ou pour créer des opérations.

### <a name="blob-metadata"></a>Métadonnées d'objet blob

En plus de **Content-Type** et **Content-Disposition**, les demandes en plusieurs parties doivent également spécifier le corps JSON approprié. Le corps JSON à envoyer varie selon le type d’opération de requête HTTP effectuée.

Les quatre principaux schémas JSON sont :

![Schémas JSON][1]

La documentation Swagger décrit en détail ces schémas de modèle.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

Découvrez plus d’informations sur l’utilisation de la documentation de référence en lisant [Comment utiliser Swagger](./how-to-use-swagger.md).

### <a name="examples"></a>Exemples

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Pour effectuer une demande **POST** qui charge un fichier texte sous forme d’objet blob et l’associe à un espace :

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

var fileContents = new StringContent("MY_BLOB.txt");
fileContents.Headers.ContentType = MediaTypeHeaderValue.Parse("text/plain");
multipartContent.Add(fileContents, "contents");

var response = await httpClient.PostAsync("spaces/blobs", multipartContent);
```

## <a name="api-endpoints"></a>Points de terminaison d’API

Les sections suivantes décrivent les points de terminaison d’API principaux liés à l’objet blob et leurs fonctionnalités.

### <a name="devices"></a>Appareils

Vous pouvez attacher des objets blob à des appareils. L’illustration suivante montre la documentation de référence Swagger pour vos API de gestion. Elle spécifie les points de terminaison d’API associés à l’appareil pour la consommation d’objets blob et tous les paramètres de chemin obligatoires à leur passer.

![Objets blob d’appareil][2]

Par exemple, pour mettre à jour ou créer un objet blob, et l’attacher à un appareil, envoyez une demande **PATCH** à :

```plaintext
YOUR_MANAGEMENT_API_URL/devices/blobs/YOUR_BLOB_ID
```

| Paramètre | Remplacer par |
| --- | --- |
| *YOUR_BLOB_ID* | ID d’objet blob souhaité |

Les demandes réussies retournent un objet JSON **DeviceBlob** dans la réponse. Les objets **DeviceBlob** sont conformes au schéma JSON suivant :

| Attribut | type | Description | Exemples |
| --- | --- | --- | --- |
| **DeviceBlobType** | Chaîne | Une catégorie d’objet blob qui peut être attachée à un appareil | `Model` et `Specification` |
| **DeviceBlobSubtype** | Chaîne | Une sous-catégorie d’objet blob qui est plus précise que **DeviceBlobType** | `PhysicalModel`, `LogicalModel`, `KitSpecification` et `FunctionalSpecification` |

> [!TIP]
> Utilisez le tableau précédent pour gérer les données de demande retournées.

### <a name="spaces"></a>Espaces

Vous pouvez également attacher des objets blob à des espaces. L’image suivante liste tous les points de terminaison d’API d’espace responsables du traitement des objets blob. Elle liste également tous les paramètres de chemin à passer à ces points de terminaison.

![Objets blob d’espace][3]

Par exemple, pour retourner un objet blob attaché à un espace, envoyez une demande **GET** à :

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs/YOUR_BLOB_ID
```

| Paramètre | Remplacer par |
| --- | --- |
| *YOUR_BLOB_ID* | ID d’objet blob souhaité |

Quand vous envoyez une demande **PATCH** au même point de terminaison, vous pouvez mettre à jour une description de métadonnées et créer une version de l’objet blob. La requête HTTP est envoyée à l’aide de la méthode **PATCH** ainsi que des métadonnées et des données de formulaire en plusieurs parties nécessaires.

Les opérations réussies retournent un objet **SpaceBlob** qui est conforme au schéma suivant. Vous pouvez l’utiliser pour consommer les données retournées.

| Attribut | type | Description | Exemples |
| --- | --- | --- | --- |
| **SpaceBlobType** | Chaîne | Une catégorie d’objet blob qui peut être attachée à un espace | `Map` et `Image` |
| **SpaceBlobSubtype** | Chaîne | Une sous-catégorie d’objet blob qui est plus précise que **SpaceBlobType** | `GenericMap`, `ElectricalMap`, `SatelliteMap` et `WayfindingMap` |

### <a name="users"></a>Utilisateurs

Vous pouvez attacher des objets blob aux modèles utilisateur (par exemple, pour associer une image de profil). L’image suivante montre les points de terminaison pertinents de l’API utilisateur et les paramètres de chemin nécessaires, comme `id` :

![Objets blob d’utilisateur][4]

Par exemple, pour extraire un objet blob attaché à un utilisateur, envoyez une demande **GET** avec les données de formulaire nécessaires à :

```plaintext
YOUR_MANAGEMENT_API_URL/users/blobs/YOUR_BLOB_ID
```

| Paramètre | Remplacer par |
| --- | --- |
| *YOUR_BLOB_ID* | ID d’objet blob souhaité |

Le JSON retourné (objets **UserBlob**) est conforme aux modèles JSON suivants :

| Attribut | type | Description | Exemples |
| --- | --- | --- | --- |
| **UserBlobType** | Chaîne | Une catégorie d’objet blob qui peut être attachée à un utilisateur | `Image` et `Video` |
| **UserBlobSubtype** |  Chaîne | Une sous-catégorie d’objet blob qui est plus précise que **UserBlobType** | `ProfessionalImage`, `VacationImage` et `CommercialVideo` |

## <a name="common-errors"></a>Erreurs courantes

Une erreur courante est de ne pas inclure pas les informations d’en-tête appropriées :

```JSON
{
    "error": {
        "code": "400.600.000.000",
        "message": "Invalid media type in first section."
    }
}
```

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la documentation de référence Swagger pour Azure Digital Twins, lisez [Utiliser Azure Digital Twins Swagger](how-to-use-swagger.md).

<!-- Images -->
[1]: media/how-to-add-blobs/blob-models.PNG
[2]: media/how-to-add-blobs/blobs-device-api.PNG
[3]: media/how-to-add-blobs/blobs-space-api.PNG
[4]: media/how-to-add-blobs/blobs-users-api.PNG
