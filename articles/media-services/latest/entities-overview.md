---
title: Développement avec des API v3 - Azure | Microsoft Docs
description: Cet article décrit les règles qui s’appliquent aux entités et API lors du développement avec Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/02/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: a5ab0b25a2a2db764854982b1a6801ce4f857dda
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58891954"
---
# <a name="developing-with-media-services-v3-apis"></a>Développement avec Media Services v3 API

Cet article décrit les règles qui s’appliquent aux entités et API lors du développement avec Media Services v3.

## <a name="naming-conventions"></a>Conventions d’affectation de noms

Les noms de ressources Azure Media Services v3 (par exemple Assets, Jobs, Transforms) sont sujets à des restrictions d’appellation par Azure Resource Manager. Conformément à Azure Resource Manager, les noms des ressources sont toujours uniques. Ainsi, vous pouvez utiliser n’importe quelle chaîne d’identificateur unique (par exemple des GUID) pour les noms de vos ressources. 

Les noms de ressources Media Services ne peuvent pas contenir : '<', '>', '%', '&', ':', '&#92;', '?', '/', '*', '+', '.', le caractère de citation unique ou tout caractère de commande. Tous les autres caractères sont autorisés. La longueur maximale d’un nom de ressources est de 260 caractères. 

Pour plus d'informations sur l'affectation de noms dans Azure Resource Manager, consultez : [Exigences en matière d'affectation de noms](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) et [Conventions d'affectation de noms](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).

## <a name="v3-api-design-principles"></a>Principes de conception de l’API v3

L’un des principes de conception clés de l’API v3 est de renforcer la sécurité de l’API. Les API v3 ne retournent pas de secrets ou d’informations d’identification lors d’opérations **Get** ou **List**. Les clés sont toujours null, vides ou purgées de la réponse. Vous devez appeler une méthode d’action distincte pour obtenir des informations d’identification ou des secrets. Les actions distinctes vous permettent de définir différentes autorisations de sécurité RBAC dans le cas où certaines API récupèrent ou affichent des secrets tandis que d’autres non. Pour plus d’informations sur la gestion de l’accès à l’aide de RBAC, consultez [Utiliser RBAC pour gérer l’accès](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest).

Voici quelques exemples :

* Ne retourne ne pas les valeurs de ContentKey dans l’opération d’obtention de la StreamingLocator.
* Ne retourne ne pas les clés de restriction dans l’opération d’obtention de la ContentKeyPolicy.
* Ne pas retourner la partie de chaîne de requête de l’URL (pour supprimer la signature) URL Jobs' HTTP d’entrée.

Consultez l’exemple [Obtenir une stratégie de clé de contenu - .NET](get-content-key-policy-dotnet-howto.md).

## <a name="long-running-operations"></a>Opérations à long terme

Les opérations marquées avec `x-ms-long-running-operation` dans les Services de média Azure [swagger fichiers](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json) sont longues opérations en cours d’exécution. 

Pour plus d’informations sur le suivi des opérations asynchrones Azure, consultez [les opérations asynchrones](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations#monitor-status-of-operation).

Media Services a les opérations de longue suivantes :

* Créer l’événement en direct
* Événement en direct de la mise à jour
* Supprimer l’événement en direct
* Démarrer l’événement en direct
* Arrêter l’événement en direct
* Réinitialisation d’événement en direct
* Créer LiveOutput
* Supprimer LiveOutput
* Créer StreamingEndpoint
* Mise à jour StreamingEndpoint
* Supprimer StreamingEndpoint
* Démarrer StreamingEndpoint
* Arrêter StreamingEndpoint
* Mise à l’échelle StreamingEndpoint

## <a name="filtering-ordering-paging-of-media-services-entities"></a>Filtrage, classement et pagination d’entités Media Services

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

### <a name="page-results"></a>Résultats de la page

Si une réponse de requête contient un grand nombre d’éléments, le service retourne une propriété « \@odata.nextLink » pour obtenir la page de résultats suivante. Celle-ci peut être utilisés pour parcourir le jeu de résultats entier. Vous ne pouvez pas configurer la taille de page. La taille de page varie selon le type d’entité. Pour plus d’informations, veuillez lire les sections suivantes.

Si des entités sont créées ou supprimées pendant la pagination de la collection, les changements sont reflétés dans les résultats retournés (si ces changements concernent la partie de la collection qui n’a pas été téléchargée). 

> [!TIP]
> Vous devez toujours utiliser le lien suivant pour énumérer la collection et ne pas dépendre d’une taille de page particulière.

### <a name="assets"></a>Éléments multimédias

#### <a name="filteringordering"></a>Filtrage/ordonnancement

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

#### <a name="pagination"></a>Pagination 

La pagination est prise en charge pour chacun des quatre ordres de tri activés. Actuellement, la taille de page est de 1 000.

##### <a name="c-example"></a>Exemple en code C#

L’exemple C# suivant montre comment énumérer tous les actifs multimédias dans le compte.

```csharp
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.Assets.ListNextAsync(currentPage.NextPageLink);
}
```

##### <a name="rest-example"></a>Exemple REST

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

### <a name="content-key-policies"></a>Stratégies de clé de contenu

#### <a name="filteringordering"></a>Filtrage/ordonnancement

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

#### <a name="pagination"></a>Pagination

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

### <a name="jobs"></a>Tâches

#### <a name="filteringordering"></a>Filtrage/ordonnancement

Le tableau suivant montre comment ces options peuvent être appliquées aux propriétés de [Travaux](https://docs.microsoft.com/rest/api/media/jobs) : 

| Nom    | Filtrer                        | Ordre |
|---------|-------------------------------|-------|
| Nom                    | eq            | croissant et décroissant|
| properties.state        | eq, ne        |                         |
| properties.created      | gt, ge, lt, le| croissant et décroissant|
| properties.lastModified | gt, ge, lt, le | croissant et décroissant| 


#### <a name="pagination"></a>Pagination

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

### <a name="streaming-locators"></a>Localisateurs de diffusion en continu

#### <a name="filteringordering"></a>Filtrage/ordonnancement

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

#### <a name="pagination"></a>Pagination

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

### <a name="streaming-policies"></a>Stratégies de diffusion en continu

#### <a name="filteringordering"></a>Filtrage/ordonnancement

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

#### <a name="pagination"></a>Pagination

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


### <a name="transform"></a>Transformer

#### <a name="filteringordering"></a>Filtrage/ordonnancement

Le tableau suivant montre comment ces options peuvent être appliquées aux propriétés de [Transformations](https://docs.microsoft.com/rest/api/media/transforms) : 

| Nom    | Filtrer                        | Ordre |
|---------|-------------------------------|-------|
| Nom                    | eq            | croissant et décroissant|
| properties.created      | gt, ge, lt, le| croissant et décroissant|
| properties.lastModified | gt, ge, lt, le | croissant et décroissant|

## <a name="next-steps"></a>Étapes suivantes

[Stream un fichier](stream-files-dotnet-quickstart.md)
