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
ms.openlocfilehash: ab4eebf56abd2d328ccf86929a043d4354ca157c
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186309"
---
# <a name="assets-in-azure-media-services"></a>Éléments multimédias dans Azure Media Services

Dans Azure Media Services, un [actif](https://docs.microsoft.com/rest/api/media/assets) contient des informations sur les fichiers numériques stockés dans Stockage Azure (notamment des données vidéo, des données audio, des images, des collections de miniatures, des pistes de texte et des fichiers de sous-titres).

Un actif multimédia est mappé à un conteneur d’objets blob dans le [compte Stockage Azure](storage-account-concept.md) et les fichiers contenus dans l’actif multimédia sont stockés sous forme d’objets blob de blocs dans ce conteneur. Media Services prend en charge les niveaux d’objets blob quand le compte utilise le stockage v2 universel (GPv2). Avec GPv2, vous pouvez déplacer les fichiers vers un [stockage Froid ou Archive](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers). Le stockage **Archive** est approprié pour archiver les fichiers sources quand ils ne sont plus nécessaires (par exemple une fois qu’ils ont été encodés).

Le niveau de stockage **Archive** est recommandé uniquement pour les fichiers sources très volumineux qui ont déjà été encodés et dont la sortie de travail d’encodage a été placée dans un conteneur d’objets blob de sortie. Les objets blob présents dans le conteneur de sortie que vous voulez associer à un élément multimédia et utiliser pour diffuser en continu ou analyser votre contenu doivent exister dans un niveau de stockage **Chaud** ou **Froid**.

### <a name="naming-blobs"></a>Nommer des objets blob

Les noms des fichiers/objets blob au sein d’une ressource doivent respecter les [exigences en matière de nom d’objet blob](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) et de [nom NTFS](https://docs.microsoft.com/windows/win32/fileio/naming-a-file). Ces exigences se justifient par le fait que les fichiers peuvent être copiés du stockage d’objets blob vers un disque NTFS local à des fins de traitement.

## <a name="upload-digital-files-into-assets"></a>Charger des fichiers numériques dans des actifs multimédias

Une fois les fichiers numériques chargés dans le stockage et associés à un élément multimédia, ils peuvent être utilisés dans des workflows Media Services d’encodage, de diffusion et d’analyse de contenu. L’un des workflows Media Services courants consiste à charger, à encoder et à diffuser en continu un fichier. Cette section décrit les étapes générales.

> [!TIP]
> Avant de commencer le développement, consultez [Développement avec les API Media Services v3](media-services-apis-overview.md) (informations sur l’accès aux API, les conventions d’affectation de noms, et ainsi de suite).

1. Utilisez l’API Media Services v3 pour créer un actif multimédia « d’entrée ». Cette opération crée un conteneur dans le compte de stockage associé à votre compte Media Services. L’API retourne le nom du conteneur (par exemple, `"container": "asset-b8d8b68a-2d7f-4d8c-81bb-8c7bbbe67ee4"`).

    Si vous disposez déjà d’un conteneur d’objets blob que vous voulez associer à un actif multimédia, vous pouvez spécifier le nom du conteneur lorsque vous créez l’élément multimédia. Actuellement, Media Services prend uniquement en charge les objets blob se trouvant à la racine du conteneur, et pas ceux dont le nom de fichier contient des chemins. Par conséquent, un conteneur portant le nom de fichier « input.mp4 » fonctionne. En revanche, un conteneur portant le nom de fichier « videos/inputs/input.mp4 » ne fonctionne pas.

    Vous pouvez utiliser l’interface de ligne de commande Azure pour effectuer un chargement directement dans n’importe quel compte de stockage et conteneur pour lesquels vous disposez de droits dans votre abonnement.

    Le nom du conteneur doive être uniques et suivre les conventions de nommage du stockage. Le nom ne doit pas nécessairement suivre la mise en forme des noms de conteneur d’actifs multimédias Media Services (Asset-GUID).

    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob
    ```
2. Obtenez une URL SAS avec des autorisations de lecture-écriture qui sera utilisée pour charger des fichiers numériques dans le conteneur d’actifs multimédias. Vous pouvez utiliser l’API Media Services pour [lister les URL de conteneurs d’actifs multimédias](https://docs.microsoft.com/rest/api/media/assets/listcontainersas).
3. Utilisez les SDK ou les API Stockage Azure (par exemple l’[API REST de stockage](../../storage/common/storage-rest-api-auth.md) ou le [SDK .NET](../../storage/blobs/storage-quickstart-blobs-dotnet.md)) pour charger des fichiers dans le conteneur d’actifs multimédias.
4. Utilisez des API Media Services v3 pour créer une transformation et un travail afin de traiter votre actif multimédia « d’entrée ». Pour plus d’informations, consultez [Transformations et travaux](transform-concept.md).
5. Diffusez en continu le contenu à partir de l’actif multimédia de « sortie ».

Pour obtenir un exemple .NET complet qui montre comment créer l’élément multimédia, obtenir une URL SAS accessible en écriture vers le conteneur de l’élément multimédia dans le stockage et charger le fichier dans le conteneur de stockage à l’aide de l’URL SAS, consultez [Créer une entrée de travail à partir d’un fichier local](job-input-from-local-file-how-to.md).

### <a name="create-a-new-asset"></a>Créer un actif multimédia

> [!NOTE]
> Les propriétés d’un élément multimédia de type DateHeure sont toujours au format UTC.

#### <a name="rest"></a>REST

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{amsAccountName}/assets/{assetName}?api-version=2018-07-01
```

Pour obtenir un exemple REST, consultez l’exemple [Créer un actif multimédia avec REST](https://docs.microsoft.com/rest/api/media/assets/createorupdate#examples).

L’exemple montre comment créer le **corps de la demande** où vous pouvez spécifier la description, le nom du conteneur, le compte de stockage et d’autres informations utiles.

#### <a name="curl"></a>cURL

```cURL
curl -X PUT \
  'https://management.azure.com/subscriptions/00000000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Media/mediaServices/amsAccountName/assets/myOutputAsset?api-version=2018-07-01' \
  -H 'Accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "properties": {
    "description": "",
  }
}'
```

#### <a name="net"></a>.NET

```csharp
 Asset asset = await client.Assets.CreateOrUpdateAsync(resourceGroupName, accountName, assetName, new Asset());
```

Pour obtenir un exemple complet, consultez [Créer une entrée de travail à partir d’un fichier local](job-input-from-local-file-how-to.md). Dans Media Services v3, il est également possible de créer une entrée de travail à partir d’URL HTTPS (consultez [Créer une entrée de travail à partir d’une URL HTTPS](job-input-from-http-how-to.md)).

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
|[Storage Service Encryption pour les données au repos](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Chiffrement côté serveur proposé par le stockage Azure, clé gérée par Azure ou par un client.|Pris en charge|Pris en charge|
|[Chiffrement de stockage côté client](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Chiffrement côté client proposé par le stockage Azure, clé gérée par un client dans un coffre de clés.|Non pris en charge|Non pris en charge|

<sup>1</sup> Bien que Media Services prenne en charge la gestion de contenu en clair/sans aucune forme de chiffrement, ce n’est pas conseillé.

<sup>2</sup> Dans Media Services v3, le chiffrement de stockage (chiffrement AES-256) est uniquement pris en charge pour la compatibilité descendante lorsque vos ressources ont été créées avec Media Services v2. Cela signifie que la version v3 fonctionne avec les ressources chiffrées du stockage existant mais qu’elle n’autorisera pas de nouvelles créations.

## <a name="filtering-ordering-paging"></a>Filtrage, tri, pagination

Consultez [Filtrage, tri et pagination des entités Media Services](entities-overview.md).

## <a name="next-steps"></a>Étapes suivantes

* [Diffuser un fichier](stream-files-dotnet-quickstart.md)
* [Utiliser un magnétoscope numérique cloud](live-event-cloud-dvr.md)
* [Différences entre Media Services v2 et v3](migrate-from-v2-to-v3.md)
