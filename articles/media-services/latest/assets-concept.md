---
title: Éléments multimédias
titleSuffix: Azure Media Services
description: Découvrez-en plus sur les éléments multimédias et leur utilisation par Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/29/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: ce32343faefbcf2484ec0b1b39f752654a2d8514
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303611"
---
# <a name="assets-in-azure-media-services"></a>Éléments multimédias dans Azure Media Services

Dans Azure Media Services, l’[actif multimédia](https://docs.microsoft.com/rest/api/media/assets) est un concept clé. C’est là que vous entrez du contenu multimédia (par exemple par le biais d’un chargement ou d’une ingestion en direct), où vous générez du contenu multimédia (à partir d’une sortie de travail) et à partir duquel vous publiez du contenu multimédia (pour le streaming). 

Un actif multimédia est mappé à un conteneur d’objets blob dans le [compte Stockage Azure](storage-account-concept.md) et les fichiers contenus dans l’actif multimédia sont stockés sous forme d’objets blob de blocs dans ce conteneur. Les actifs multimédias contiennent des informations sur les fichiers numériques stockés dans Stockage Azure (notamment des données vidéo, des données audio, des images, des collections de miniatures, des pistes de texte et des fichiers de sous-titres).

Media Services prend en charge les niveaux d’objets blob quand le compte utilise le stockage v2 universel (GPv2). Avec GPv2, vous pouvez déplacer les fichiers vers un [stockage Froid ou Archive](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers). Le stockage **Archive** est approprié pour archiver les fichiers sources quand ils ne sont plus nécessaires (par exemple une fois qu’ils ont été encodés).

Le niveau de stockage **Archive** est recommandé uniquement pour les fichiers sources très volumineux qui ont déjà été encodés et dont la sortie de travail d’encodage a été placée dans un conteneur d’objets blob de sortie. Les objets blob présents dans le conteneur de sortie que vous voulez associer à un élément multimédia et utiliser pour diffuser en continu ou analyser votre contenu doivent exister dans un niveau de stockage **Chaud** ou **Froid**.

## <a name="naming"></a>Dénomination 

### <a name="assets"></a>Éléments multimédias

Les noms des éléments multimédias doivent être uniques. Les noms de ressources Media Services v3 (par exemple Assets, Jobs, Transforms) sont sujets à des restrictions d'affectation de noms d'Azure Resource Manager. Pour plus d'informations, consultez [Conventions d'affectation de noms](media-services-apis-overview.md#naming-conventions).

### <a name="blobs"></a>Objets blob

Les noms des fichiers/objets blob au sein d’une ressource doivent respecter les [exigences en matière de nom d’objet blob](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) et de [nom NTFS](https://docs.microsoft.com/windows/win32/fileio/naming-a-file). Ces exigences se justifient par le fait que les fichiers peuvent être copiés du stockage d’objets blob vers un disque NTFS local à des fins de traitement.

## <a name="map-v3-asset-properties-to-v2"></a>Mapper les propriétés de l’élément multimédia v3 à v2

Le tableau suivant montre comment les propriétés de l’[élément multimédia](https://docs.microsoft.com/rest/api/media/assets/createorupdate#asset)de v3 sont mappées aux propriétés de l’élément multimédia de v2.

|Propriétés de v3|Propriétés de v2|
|---|---|
|`id` : (unique) chemin d’accès complet à Azure Resource Manager, voir les exemples dans [Élément multimédia](https://docs.microsoft.com/rest/api/media/assets/createorupdate)||
|`name` : (unique) consultez [Convention d’affectation de noms](media-services-apis-overview.md#naming-conventions) ||
|`alternateId`|`AlternateId`|
|`assetId`|`Id` : valeur (unique) commençant par le préfixe `nb:cid:UUID:`.|
|`created`|`Created`|
|`description`|`Name`|
|`lastModified`|`LastModified`|
|`storageAccountName`|`StorageAccountName`|
|`storageEncryptionFormat`| `Options` (options de création)|
|`type`||

## <a name="storage-side-encryption"></a>Chiffrement côté stockage

Pour protéger vos éléments au repos, les ressources doivent être chiffrées par le chiffrement côté stockage. Le tableau suivant montre comment le chiffrement côté stockage fonctionne dans Media Services :

|Option de chiffrement|Description|Media Services v2|Media Services v3|
|---|---|---|---|
|Chiffrement du stockage de Media Services|Chiffrement AES-256, clé gérée par Media Services.|Pris en charge<sup>(1)</sup>|Non pris en charge<sup>(2)</sup>|
|[Storage Service Encryption pour les données au repos](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Chiffrement côté serveur proposé par le stockage Azure, clé gérée par Azure ou par un client.|Prise en charge|Prise en charge|
|[Chiffrement de stockage côté client](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Chiffrement côté client proposé par le stockage Azure, clé gérée par un client dans un coffre de clés.|Non pris en charge|Non pris en charge|

<sup>1</sup> Bien que Media Services prenne en charge la gestion de contenu en clair/sans aucune forme de chiffrement, ce n’est pas conseillé.

<sup>2</sup> Dans Media Services v3, le chiffrement de stockage (chiffrement AES-256) est uniquement pris en charge pour la compatibilité descendante lorsque vos ressources ont été créées avec Media Services v2. Cela signifie que la version v3 fonctionne avec les ressources chiffrées du stockage existant mais qu’elle n’autorisera pas de nouvelles créations.

## <a name="next-steps"></a>Étapes suivantes

[Gérer les actifs multimédias dans Media Services](manage-asset-concept.md)

## <a name="see-also"></a>Voir aussi

[Différences entre Media Services v2 et v3](migrate-from-v2-to-v3.md)
