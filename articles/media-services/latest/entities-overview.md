---
title: Filtrage, classement, la pagination des entités Media Services - Azure | Microsoft Docs
description: Cet article décrit le filtrage, le classement et la pagination d’entités Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/08/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 28c880e8709074d808a41d9920361eaa2b20ecc4
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/10/2019
ms.locfileid: "59470855"
---
# <a name="filtering-ordering-paging-of-media-services-entities"></a>Filtrage, classement et pagination d’entités Media Services

Media Services prend en charge les options de requête OData suivantes pour les entités Media Services v3 : 

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

Les propriétés d’entités de type DateHeure sont toujours au format UTC.

## <a name="page-results"></a>Résultats de la page

Si une réponse de requête contient un grand nombre d’éléments, le service retourne une propriété « \@odata.nextLink » pour obtenir la page de résultats suivante. Celle-ci peut être utilisés pour parcourir le jeu de résultats entier. Vous ne pouvez pas configurer la taille de page. La taille de page varie selon le type d’entité. Pour plus d’informations, veuillez lire les sections suivantes.

Si des entités sont créées ou supprimées pendant la pagination de la collection, les changements sont reflétés dans les résultats retournés (si ces changements concernent la partie de la collection qui n’a pas été téléchargée). 

> [!TIP]
> Vous devez toujours utiliser le lien suivant pour énumérer la collection et ne pas dépendre d’une taille de page particulière.

## <a name="assets"></a>Éléments multimédias

### <a name="filteringordering"></a>Filtrage/ordonnancement

Le tableau suivant montre comment les options de filtrage et de classement peuvent être appliquées aux propriétés de [Ressource](https://docs.microsoft.com/rest/api/media/assets) : 

|Nom|Filtrer|Ordre|
|---|---|---|
|id|||
|Nom|eq, gt, lt| croissant et décroissant|
|properties.alternateId |eq||
|properties.assetId |eq||
|properties.container |||
|properties.created| eq, gt, lt| croissant et décroissant|
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

## <a name="content-key-policies"></a>Stratégies de clé de contenu

### <a name="filteringordering"></a>Filtrage/ordonnancement

Le tableau suivant montre comment ces options peuvent être appliquées aux propriétés de [Stratégies de clé de contenu](https://docs.microsoft.com/rest/api/media/contentkeypolicies) : 

|Nom|Filtrer|Ordre|
|---|---|---|
|id|||
|Nom|eq, ne, ge, le, gt, lt|croissant et décroissant|
|properties.created |eq, ne, ge, le,  gt, lt|croissant et décroissant|
|properties.description |eq, ne, ge, le, gt, lt||
|properties.lastModified|eq, ne, ge, le, gt, lt|croissant et décroissant|
|properties.options |||
|properties.policyId|eq, ne||
|Type|||

### <a name="pagination"></a>Pagination

La pagination est prise en charge pour chacun des quatre ordres de tri activés. Actuellement, la taille de page est de 10.

L’exemple C# suivant montre comment énumérer toutes les **Stratégies de clé de contenu** dans le compte.

```csharp
var firstPage = await MediaServicesArmClient.ContentKeyPolicies.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.ContentKeyPolicies.ListNextAsync(currentPage.NextPageLink);
}
```

Pour obtenir des exemples REST, consultez [Stratégies de clé de contenu - Liste](https://docs.microsoft.com/rest/api/media/contentkeypolicies/list)

## <a name="jobs"></a>Tâches

### <a name="filteringordering"></a>Filtrage/ordonnancement

Le tableau suivant montre comment ces options peuvent être appliquées aux propriétés de [Travaux](https://docs.microsoft.com/rest/api/media/jobs) : 

| Nom    | Filtrer                        | Ordre |
|---------|-------------------------------|-------|
| Nom                    | eq            | croissant et décroissant|
| properties.state        | eq, ne        |                         |
| properties.created      | gt, ge, lt, le| croissant et décroissant|
| properties.lastModified | gt, ge, lt, le | croissant et décroissant| 

### <a name="pagination"></a>Pagination

La pagination des travaux est prise en charge dans Media Services v3.

L’exemple C# suivant montre comment énumérer tous les travaux dans le compte.

```csharp            
List<string> jobsToDelete = new List<string>();
var pageOfJobs = client.Jobs.List(config.ResourceGroup, config.AccountName, "Encode");

bool exit;
do
{
    foreach (Job j in pageOfJobs)
    {
        jobsToDelete.Add(j.Name);
    }

    if (pageOfJobs.NextPageLink != null)
    {
        pageOfJobs = client.Jobs.ListNext(pageOfJobs.NextPageLink);
        exit = false;
    }
    else
    {
        exit = true;
    }
}
while (!exit);

```

Pour obtenir des exemples REST, consultez [Travaux - Liste](https://docs.microsoft.com/rest/api/media/jobs/list)

## <a name="streaming-locators"></a>Localisateurs de diffusion en continu

### <a name="filteringordering"></a>Filtrage/ordonnancement

Le tableau suivant montre comment ces options peuvent être appliquées aux propriétés du localisateur de diffusion en continu : 

|Nom|Filtrer|Ordre|
|---|---|---|
|id |||
|Nom|eq, ne, ge, le, gt, lt|croissant et décroissant|
|properties.alternativeMediaId  |||
|properties.assetName   |||
|properties.contentKeys |||
|properties.created |eq, ne, ge, le,  gt, lt|croissant et décroissant|
|properties.defaultContentKeyPolicyName |||
|properties.endTime |eq, ne, ge, le, gt, lt|croissant et décroissant|
|properties.startTime   |||
|properties.streamingLocatorId  |||
|properties.streamingPolicyName |||
|Type   |||

### <a name="pagination"></a>Pagination

La pagination est prise en charge pour chacun des quatre ordres de tri activés. Actuellement, la taille de page est de 10.

L’exemple C# suivant montre comment énumérer tous les localisateurs de diffusion en continu dans le compte.

```csharp
var firstPage = await MediaServicesArmClient.StreamingLocators.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingLocators.ListNextAsync(currentPage.NextPageLink);
}
```

Pour obtenir des exemples REST, consultez [Streaming Locators - List](https://docs.microsoft.com/rest/api/media/streaminglocators/list) (Localisateurs de diffusion en continu : lister)

## <a name="streaming-policies"></a>Stratégies de diffusion en continu

### <a name="filteringordering"></a>Filtrage/ordonnancement

Le tableau suivant montre comment ces options peuvent être appliquées aux propriétés de StreamingPolicy : 

|Nom|Filtrer|Ordre|
|---|---|---|
|id|||
|Nom|eq, ne, ge, le, gt, lt|croissant et décroissant|
|properties.commonEncryptionCbcs|||
|properties.commonEncryptionCenc|||
|properties.created |eq, ne, ge, le,  gt, lt|croissant et décroissant|
|properties.defaultContentKeyPolicyName |||
|properties.envelopeEncryption|||
|properties.noEncryption|||
|Type|||

### <a name="pagination"></a>Pagination

La pagination est prise en charge pour chacun des quatre ordres de tri activés. Actuellement, la taille de page est de 10.

L’exemple C# suivant montre comment énumérer toutes les stratégies StreamingPolicy dans le compte.

```csharp
var firstPage = await MediaServicesArmClient.StreamingPolicies.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingPolicies.ListNextAsync(currentPage.NextPageLink);
}
```

Pour obtenir des exemples REST, consultez [Stratégies de streaming - Liste](https://docs.microsoft.com/rest/api/media/streamingpolicies/list)

## <a name="transform"></a>Transformer

### <a name="filteringordering"></a>Filtrage/ordonnancement

Le tableau suivant montre comment ces options peuvent être appliquées aux propriétés de [Transformations](https://docs.microsoft.com/rest/api/media/transforms) : 

| Nom    | Filtrer                        | Ordre |
|---------|-------------------------------|-------|
| Nom                    | eq            | croissant et décroissant|
| properties.created      | gt, ge, lt, le| croissant et décroissant|
| properties.lastModified | gt, ge, lt, le | croissant et décroissant|

## <a name="next-steps"></a>Étapes suivantes

[Stream un fichier](stream-files-dotnet-quickstart.md)
