---
title: Éléments multimédias dans Azure Media Services | Microsoft Docs
description: Cet article explique ce que sont les éléments multimédias et comment ils sont utilisés par Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/15/2018
ms.author: juliako
ms.openlocfilehash: 62cc4634a0f76b0562d5b3c1355a7442fc5cf989
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49985231"
---
# <a name="assets"></a>Éléments multimédias

Un **élément multimédia** contient des fichiers numériques (y compris vidéo, audio, images, collections de miniatures, légendes et fichiers de sous-titres) et les métadonnées associées à ces fichiers. Une fois les fichiers numériques chargés dans un élément multimédia, ils peuvent être utilisés dans des flux de travail de diffusion et d’encodage Media Services.

Un élément multimédia est mappé à un conteneur d’objets blob dans le [compte de stockage Azure](storage-account-concept.md) et les fichiers de l’élément multimédia sont stockés en tant qu’objets blob de blocs dans ce conteneur. Vous pouvez interagir avec les fichiers d’éléments multimédias dans les conteneurs à l’aide des clients SDK Stockage.

Azure Media Services prend en charge les niveaux d’objet blob lorsque le compte utilise le stockage à usage général v2 (GPv2). Avec GPv2, vous pouvez déplacer les fichiers vers un stockage froid. Le stockage froid est approprié pour archiver les fichiers sources quand ils ne sont plus nécessaires (par exemple une fois qu’ils ont été encodés).

Dans Media Services v3, l’entrée de travail peut être créée à partir d’éléments multimédias ou d’URL HTTP(s). Pour créer un élément multimédia qui peut être utilisé comme entrée pour votre travail, consultez [Créer une entrée de travail à partir d’un fichier local](job-input-from-local-file-how-to.md).

Consultez également les articles sur les [comptes de stockage dans Media Services](storage-account-concept.md) et les [transformations et travaux](transform-concept.md).

## <a name="asset-definition"></a>Définition d’actif multimédia

Le tableau suivant présente les propriétés de l’actif multimédia ainsi que leurs définitions.

|NOM|type|Description|
|---|---|---|
|id|chaîne|ID de ressource complet pour la ressource.|
|Nom|chaîne|Nom de la ressource.|
|properties.alternateId |chaîne|Autre ID de l’actif multimédia.|
|properties.assetId |chaîne|ID de l’actif multimédia.|
|properties.container |chaîne|Nom du conteneur d’objets blob d’actifs multimédias.|
|properties.created |chaîne|Date de création de l’actif multimédia.|
|properties.description |chaîne|Description de l’actif multimédia.|
|properties.lastModified |chaîne|Date de dernière modification de l’actif multimédia.|
|properties.storageAccountName |chaîne|Nom du compte de stockage.|
|properties.storageEncryptionFormat |AssetStorageEncryptionFormat |Format de chiffrement de l’actif multimédia. None ou MediaStorageEncryption.|
|Type|chaîne|Type de la ressource.|

Pour obtenir la définition complète, consultez [Actifs](https://docs.microsoft.com/rest/api/media/assets).

## <a name="filtering-ordering-paging"></a>Filtrage, tri, pagination

Media Services prend en charge les options de requête OData suivantes pour les actifs multimédias : 

* $filter 
* $orderby 
* $top 
* $skiptoken 

Description des opérateurs :

* Eq = est égal à
* Ne = n’est pas égal à
* Ge = est supérieur ou égal à
* Le = est inférieur ou égal à
* Gt = est supérieur à
* Lt = est inférieur à

### <a name="filteringordering"></a>Filtrage/ordonnancement

Le tableau suivant montre comment ces options peuvent être appliquées aux propriétés d’actifs multimédias : 

|NOM|Filtrer|Ordre|
|---|---|---|
|id|||
|Nom|Pris en charge : Eq, Gt, Lt|Pris en charge : croissant et décroissant|
|properties.alternateId |Pris en charge : Eq||
|properties.assetId |Pris en charge : Eq||
|properties.container |||
|properties.created|Pris en charge : Eq, Gt, Lt| Pris en charge : croissant et décroissant|
|properties.description |||
|properties.lastModified |||
|properties.storageAccountName |||
|properties.storageEncryptionFormat | ||
|Type|||

L’exemple C# suivant filtre sur la date de création :

```csharp
var odataQuery = new ODataQuery<Asset>("properties/created lt 2018-05-11T17:39:08.387Z");
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName, odataQuery);
```

### <a name="pagination"></a>Pagination

La pagination est prise en charge pour chacun des quatre ordres de tri activés. Actuellement, la taille de page est de 1 000.

> [!TIP]
> Vous devez toujours utiliser le lien suivant pour énumérer la collection et ne pas dépendre d’une taille de page particulière.

Si une réponse de requête contient un grand nombre d’éléments, le service retourne une propriété « \@odata.nextLink » pour obtenir la page de résultats suivante. Celle-ci peut être utilisés pour parcourir le jeu de résultats entier. Vous ne pouvez pas configurer la taille de page. 

Si des actifs multimédias sont créés ou supprimés pendant la pagination dans la collection, les changements sont reflétés dans les résultats retournés (si ces changements concernent la partie de la collection qui n’a pas été téléchargée). 

L’exemple C# suivant montre comment énumérer tous les actifs multimédias dans le compte.

```csharp
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.Assets.ListNextAsync(currentPage.NextPageLink);
}
```

Pour obtenir des exemples REST, consultez [Actifs multimédias - Liste](https://docs.microsoft.com/rest/api/media/assets/list).

## <a name="storage-side-encryption"></a>Chiffrement côté stockage

Pour protéger vos éléments au repos, les ressources doivent être chiffrées par le chiffrement côté stockage. Le tableau suivant montre comment le chiffrement côté stockage fonctionne dans Media Services :

|Option de chiffrement|Description|Media Services v2|Media Services v3|
|---|---|---|---|
|Chiffrement du stockage de Media Services|Chiffrement AES-256, clé gérée par Media Services|Pris en charge<sup>(1)</sup>|Non pris en charge<sup>(2)</sup>|
|[Storage Service Encryption pour les données au repos](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Chiffrement côté serveur proposé par le stockage Azure, clé gérée par Azure ou par un client|Pris en charge|Pris en charge|
|[Chiffrement de stockage côté client](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Chiffrement côté client proposé par le stockage Azure, clé gérée par un client dans un coffre de clés|Non pris en charge|Non pris en charge|

<sup>1</sup> Bien que Media Services prenne en charge la gestion de contenu en clair/sans aucune forme de chiffrement, ce n’est pas conseillé.

<sup>2</sup> Dans Media Services v3, le chiffrement de stockage (chiffrement AES-256) est uniquement pris en charge pour la compatibilité descendante lorsque vos ressources ont été créées avec Media Services v2. Cela signifie que la version v3 fonctionne avec les ressources chiffrées du stockage existant mais qu’elle n’autorisera pas de nouvelles créations.

## <a name="next-steps"></a>Étapes suivantes

[Diffuser un fichier](stream-files-dotnet-quickstart.md)
