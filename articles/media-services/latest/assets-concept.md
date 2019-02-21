---
title: Éléments multimédias dans Media Services - Azure | Microsoft Docs
description: Cet article explique ce que sont les éléments multimédias et comment ils sont utilisés par Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/19/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 2ec2ddbac5d0368aaf1b46208c9ebb44bf12a622
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56447308"
---
# <a name="assets"></a>Éléments multimédias

Dans Azure Media Services, un [actif multimédia](https://docs.microsoft.com/rest/api/media/assets) contient des fichiers numériques (notamment des données vidéo, des données audio, des images, des collections de miniatures, des pistes de texte et des fichiers de sous-titres) et les métadonnées associées à ces fichiers. Une fois les fichiers numériques chargés dans un actif multimédia, ils peuvent être utilisés dans des workflows Media Services d’encodage, de diffusion et d’analyse de contenu. Pour plus d’informations, consultez la section [Charger des fichiers numériques dans des actifs multimédias](#upload-digital-files-into-assets), ci-dessous.

Un actif multimédia est mappé à un conteneur d’objets blob dans le [compte Stockage Azure](storage-account-concept.md) et les fichiers contenus dans l’actif multimédia sont stockés sous forme d’objets blob de blocs dans ce conteneur. Media Services prend en charge les niveaux d’objets blob quand le compte utilise le stockage v2 universel (GPv2). Avec GPv2, vous pouvez déplacer les fichiers vers un [stockage Froid ou Archive](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers). Le stockage **Archive** est approprié pour archiver les fichiers sources quand ils ne sont plus nécessaires (par exemple une fois qu’ils ont été encodés).

Le niveau de stockage **Archive** est recommandé uniquement pour les fichiers sources très volumineux qui ont déjà été encodés et dont la sortie de travail d’encodage a été placée dans un conteneur d’objets blob de sortie. Les objets blob présents dans le conteneur de sortie que vous voulez associer à un actif multimédia et utiliser pour diffuser en continu ou analyser votre contenu doivent exister dans un niveau de stockage **Chaud** ou **Froid**.

> [!NOTE]
> Les propriétés d’élément multimédia de type DateHeure sont toujours au format UTC.

## <a name="upload-digital-files-into-assets"></a>Charger des fichiers numériques dans des actifs multimédias

L’un des workflows Media Services courants consiste à charger, à encoder et à diffuser en continu un fichier. Cette section décrit les étapes générales.

1. Utilisez l’API Media Services v3 pour créer un actif multimédia « d’entrée ». Cette opération crée un conteneur dans le compte de stockage associé à votre compte Media Services. L’API retourne le nom du conteneur (par exemple, `"container": "asset-b8d8b68a-2d7f-4d8c-81bb-8c7bbbe67ee4"`).
   
    Si vous disposez déjà d’un conteneur d’objets blob que vous voulez associer à un actif multimédia, vous pouvez spécifier le nom du conteneur lors de la création de l’actif multimédia. Actuellement, Media Services prend uniquement en charge les objets blob se trouvant à la racine du conteneur, et pas ceux dont le nom de fichier contient des chemins. Par conséquent, un conteneur portant le nom de fichier « input.mp4 » fonctionne. En revanche, un conteneur portant le nom de fichier « videos/inputs/input.mp4 » ne fonctionne pas.

    Vous pouvez utiliser l’interface de ligne de commande Azure pour effectuer un chargement directement dans n’importe quel compte de stockage et conteneur pour lesquels vous disposez de droits dans votre abonnement. <br/>Le nom du conteneur doive être uniques et suivre les conventions de nommage du stockage. Le nom ne doit pas nécessairement suivre la mise en forme des noms de conteneur d’actifs multimédias Media Services (Asset-GUID). 
    
    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob
    ```
2. Obtenez une URL SAS avec des autorisations de lecture-écriture qui sera utilisée pour charger des fichiers numériques dans le conteneur d’actifs multimédias. Vous pouvez utiliser l’API Media Services pour [lister les URL de conteneurs d’actifs multimédias](https://docs.microsoft.com/rest/api/media/assets/listcontainersas).
3. Utilisez les API ou les SDK de Stockage Azure (par exemple, [l’API REST de stockage](../../storage/common/storage-rest-api-auth.md), le [SDK JAVA](../../storage/blobs/storage-quickstart-blobs-java-v10.md) ou [le SDK .NET](../../storage/blobs/storage-quickstart-blobs-dotnet.md)) pour charger des fichiers dans le conteneur d’actifs multimédias. 
4. Utilisez des API Media Services v3 pour créer une transformation et un travail afin de traiter votre actif multimédia « d’entrée ». Pour plus d’informations, consultez [Transformations et travaux](transform-concept.md).
5. Diffusez en continu le contenu à partir de l’actif multimédia de « sortie ».

Pour obtenir un exemple .NET complet qui montre comment créer l’actif multimédia, obtenir une URL SAS accessible en écriture vers le conteneur de l’actif multimédia dans le stockage et charger le fichier dans le conteneur de stockage à l’aide de l’URL SAS, consultez [Créer une entrée de travail à partir d’un fichier local](job-input-from-local-file-how-to.md).

### <a name="create-a-new-asset"></a>Créer un actif multimédia

#### <a name="rest"></a>REST

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{amsAccountName}/assets/{assetName}?api-version=2018-07-01
```

Pour obtenir un exemple REST, consultez l’exemple [Créer un actif multimédia avec REST](https://docs.microsoft.com/rest/api/media/assets/createorupdate#examples).

L’exemple montre comment créer le **corps de la demande** où vous pouvez spécifier des informations utiles comme la description, le nom du conteneur, le compte de stockage et d’autres informations.

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

## <a name="filtering-ordering-paging"></a>Filtrage, tri, pagination

Consultez [Filtrage, tri et pagination des entités Media Services](entities-overview.md).

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

* [Diffuser un fichier](stream-files-dotnet-quickstart.md)
* [Utiliser un magnétoscope numérique cloud](live-event-cloud-dvr.md)
* [Différences entre Media Services v2 et v3](migrate-from-v2-to-v3.md)
