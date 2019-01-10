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
ms.date: 01/01/2018
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 8507d51f0d4d49d89fc24b38ed73df7488261daa
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/02/2019
ms.locfileid: "53969573"
---
# <a name="assets"></a>Éléments multimédias

Dans Azure Media Services, un [actif multimédia](https://docs.microsoft.com/rest/api/media/assets) contient des fichiers numériques (notamment des données vidéo, des données audio, des images, des collections de miniatures, des pistes de texte et des fichiers de sous-titres) et les métadonnées associées à ces fichiers. Une fois les fichiers numériques chargés dans un actif multimédia, ils peuvent être utilisés dans des workflows Media Services d’encodage, de diffusion et d’analyse de contenu. Pour plus d’informations, consultez la section [Charger des fichiers numériques dans des actifs multimédias](#upload-digital-files-into-assets), ci-dessous.

Un actif multimédia est mappé à un conteneur d’objets blob dans le [compte Stockage Azure](storage-account-concept.md) et les fichiers contenus dans l’actif multimédia sont stockés sous forme d’objets blob de blocs dans ce conteneur. Media Services prend en charge les niveaux d’objets blob quand le compte utilise le stockage v2 universel (GPv2). Avec GPv2, vous pouvez déplacer les fichiers vers un [stockage Froid ou Archive](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers). Le stockage **Archive** est approprié pour archiver les fichiers sources quand ils ne sont plus nécessaires (par exemple une fois qu’ils ont été encodés).

Le niveau de stockage **Archive** est recommandé uniquement pour les fichiers sources très volumineux qui ont déjà été encodés et dont la sortie de travail d’encodage a été placée dans un conteneur d’objets blob de sortie. Les objets blob présents dans le conteneur de sortie que vous voulez associer à un actif multimédia et utiliser pour diffuser en continu ou analyser votre contenu doivent exister dans un niveau de stockage **Chaud** ou **Froid**.

## <a name="asset-definition"></a>Définition d’actif multimédia

Le tableau suivant présente les propriétés de l’actif multimédia ainsi que leurs définitions.

|NOM|Description|
|---|---|
|id|ID de ressource complet pour la ressource.|
|Nom|Nom de la ressource.|
|properties.alternateId |Autre ID de l’actif multimédia.|
|properties.assetId |ID de l’actif multimédia.|
|properties.container |Nom du conteneur d’objets blob d’actifs multimédias.|
|properties.created |Date de création de l’actif multimédia.<br/> La valeur date/heure est toujours au format UTC.|
|properties.description|Description de l’actif multimédia.|
|properties.lastModified |Date de dernière modification de l’actif multimédia. <br/> La valeur date/heure est toujours au format UTC.|
|properties.storageAccountName |Nom du compte de stockage.|
|properties.storageEncryptionFormat |Format de chiffrement de l’actif multimédia. None ou MediaStorageEncryption.|
|Type|Type de la ressource.|

Pour obtenir la définition complète, consultez [Actifs multimédias](https://docs.microsoft.com/rest/api/media/assets).

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

> [!TIP]
> Pour obtenir un exemple .NET complet qui montre comment créer l’actif multimédia, obtenir une URL SAS accessible en écriture vers le conteneur de l’actif multimédia dans le stockage et charger le fichier dans le conteneur de stockage à l’aide de l’URL SAS, consultez [Créer une entrée de travail à partir d’un fichier local](job-input-from-local-file-how-to.md).

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

Media Services prend en charge les options de requête OData suivantes pour les actifs multimédias : 

* $filter 
* $orderby 
* $top 
* $skiptoken 

Description des opérateurs :

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
|Nom|Prend en charge : Eq, Gt, Lt|Prend en charge : croissant et décroissant|
|properties.alternateId |Prend en charge : Eq||
|properties.assetId |Prend en charge : Eq||
|properties.container |||
|properties.created|Prend en charge : Eq, Gt, Lt| Prend en charge : croissant et décroissant|
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

Si des actifs multimédias sont créés ou supprimés pendant la pagination de la collection, les changements sont reflétés dans les résultats retournés (si ces changements concernent la partie de la collection qui n’a pas été téléchargée). 

#### <a name="c-example"></a>Exemple en code C#

L’exemple C# suivant montre comment énumérer tous les actifs multimédias dans le compte.

```csharp
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.Assets.ListNextAsync(currentPage.NextPageLink);
}
```

#### <a name="rest-example"></a>Exemple REST

Prenons l’exemple suivant où $skiptoken est utilisé. Veillez à remplacer *amstestaccount* par le nom de votre compte et à définir la valeur *api-version* avec la version la plus récente.

Si vous demandez une liste d’actifs multimédias comme suit :

```
GET  https://management.azure.com/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01 HTTP/1.1
x-ms-client-request-id: dd57fe5d-f3be-4724-8553-4ceb1dbe5aab
Content-Type: application/json; charset=utf-8
```

Vous devez obtenir une réponse similaire à celle-ci :

```
HTTP/1.1 200 OK
 
{
"value":[
{
"name":"Asset 0","id":"/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 0","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-5a4f-470a-9d81-6037d7c23eff","created":"2018-12-11T22:12:44.98Z","lastModified":"2018-12-11T22:15:48.003Z","container":"asset-98d07299-5a4f-470a-9d81-6037d7c23eff","storageAccountName":"amsdevc1stoaccount11","storageEncryptionFormat":"None"
}
},
// lots more assets
{
"name":"Asset 517","id":"/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 517","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-912e-447b-a1ed-0f723913b20d","created":"2018-12-11T22:14:08.473Z","lastModified":"2018-12-11T22:19:29.657Z","container":"asset-fd05a503-912e-447b-a1ed-0f723913b20d","storageAccountName":"amsdevc1stoaccount11","storageEncryptionFormat":"None"
}
}
],"@odata.nextLink":"https:// management.azure.com/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517"
}
```

Vous devez ensuite demander la page suivante en envoyant une requête GET pour :

```
https://management.azure.com/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517
```

Pour obtenir des exemples REST, consultez [Actifs multimédias - Liste](https://docs.microsoft.com/rest/api/media/assets/list).

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

[Différences entre Media Services v2 et v3](migrate-from-v2-to-v3.md)
