---
title: Gérer les actifs multimédias dans Azure Media Services
titleSuffix: Azure Media Services
description: Un actif multimédia est l’endroit où vous entrez du contenu multimédia (par exemple via un chargement ou une ingestion en direct), où vous sortez du contenu multimédia (d’une sortie de travail) et à partir duquel vous publiez du contenu multimédia (pour le streaming). Cette rubrique donne une vue d’ensemble de la façon de créer un actif multimédia et de charger des fichiers.
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
ms.openlocfilehash: b1ec4ee3d7a51c2a21a5bbd8888ea4662cf78bf5
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78304155"
---
# <a name="manage-assets"></a>Gérer les actifs multimédias

Dans Azure Media Services, un [actif multimédia](https://docs.microsoft.com/rest/api/media/assets) est l’endroit où vous 

* chargez des fichiers multimédias,
* ingérez et archivez des streams en direct,
* sortez les résultats de l’encodage d’un travail d’analytique,
* publiez du contenu multimédia à des fins de streaming, 
* téléchargez des fichiers à partir d’un actif multimédia.

Cette rubrique donne une vue d’ensemble de la façon de charger des fichiers dans un actif multimédia et d’effectuer d’autres opérations courantes. Elle fournit également des liens vers des exemples de code et des rubriques connexes.

## <a name="prerequisite"></a>Configuration requise 

Avant de commencer à développer, passez en revue ce qui suit :

* [Concepts](concepts-overview.md)
* [Développement avec les API Media Services v3](media-services-apis-overview.md) (informations sur l’accès aux API, les conventions de nommage, et ainsi de suite) 

## <a name="upload-media-files-into-an-asset"></a>Charger des fichiers multimédias dans un actif multimédia

Une fois les fichiers numériques chargés dans le stockage et associés à un élément multimédia, ils peuvent être utilisés dans des workflows Media Services d’encodage, de diffusion et d’analyse de contenu. L’un des workflows Media Services courants consiste à charger, à encoder et à diffuser en continu un fichier. Cette section décrit les étapes générales.

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

### <a name="see-also"></a>Voir aussi

* [Créer une entrée de travail à partir d’un fichier local](job-input-from-local-file-how-to.md)
* [Créer une entrée de travail à partir d’une URL HTTPS](job-input-from-http-how-to.md)

## <a name="ingest-and-archive-live-streams-into-an-asset"></a>Ingérer et archiver des streams en direct dans un actif multimédia

Dans Media Services, un objet [Sortie en direct](https://docs.microsoft.com/rest/api/media/liveoutputs) fonctionne comme un magnétoscope numérique qui capture et enregistre votre stream en direct dans un actif multimédia dans votre compte Media Services. Le contenu enregistré est conservé dans le conteneur défini par la ressource d’[actif multimédia](https://docs.microsoft.com/rest/api/media/assets).

Pour plus d'informations, consultez les pages suivantes :

* [Utiliser un magnétoscope numérique cloud](live-event-cloud-dvr.md)
* [Tutoriel sur le streaming en direct](stream-live-tutorial-with-api.md)

## <a name="output-the-results-of-a-job-to-an-asset"></a>Générer les résultats d’un travail vers un actif multimédia

Dans Media Services, lors du traitement de vos vidéos (par exemple l’encodage ou l’analyse), vous devez créer un [actif multimédia](assets-concept.md) de sortie où stocker le résultat de votre [travail](transforms-jobs-concept.md).

Pour plus d'informations, consultez les pages suivantes :

* [Encodage d’une vidéo](encoding-concept.md)
* [Créer une entrée de travail à partir d’un fichier local](job-input-from-local-file-how-to.md)

## <a name="publish-an-asset-for-streaming"></a>Publier un actif multimédia à des fins de streaming

Pour publier un actif multimédia à des fins de streaming, vous devez créer un [Localisateur de streaming](streaming-locators-concept.md). Le localisateur de streaming doit connaître le nom de l’actif multimédia que vous souhaitez publier. 

Pour plus d'informations, consultez les pages suivantes :

[Tutoriel : Télécharger, encoder et diffuser des vidéos avec Media Services v3](stream-files-tutorial-with-api.md)

## <a name="download-results-of-a-job-from-an-output-asset"></a>Télécharger les résultats d’un travail à partir d’un actif multimédia de sortie

Vous pouvez ensuite télécharger ces résultats de votre travail dans un dossier local à l’aide des API de stockage et Media Services. 

Consultez l’exemple de [fichiers de téléchargement](download-results-howto.md).

## <a name="filtering-ordering-paging"></a>Filtrage, tri, pagination

Consultez [Filtrage, tri et pagination des entités Media Services](entities-overview.md).

## <a name="next-steps"></a>Étapes suivantes

Consultez les exemples de code complets qui montrent comment charger, encoder, analyser, diffuser en streaming en direct et à la demande : 

* [Java](https://docs.microsoft.com/samples/azure-samples/media-services-v3-java/azure-media-services-v3-samples-using-java/), 
* [.NET](https://docs.microsoft.com/samples/azure-samples/media-services-v3-dotnet/azure-media-services-v3-samples-using-net/), 
* [REST](https://docs.microsoft.com/samples/azure-samples/media-services-v3-rest-postman/azure-media-services-postman-collection/).
