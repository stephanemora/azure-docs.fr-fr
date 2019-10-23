---
title: Filtrage, classement et pagination des d’entités Media Services – Azure | Microsoft Docs
description: Cet article décrit le filtrage, le classement et la pagination d’entités Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/11/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: ed509ac8fea43a9c011bbbf76c1dc433cd78d43c
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298955"
---
# <a name="filtering-ordering-paging-of-media-services-entities"></a>Filtrage, classement et pagination d’entités Media Services

Cette rubrique décrit les options de requête OData et la prise en charge de la pagination disponibles dans la liste des entités Azure Media Services V3.

## <a name="considerations"></a>Considérations

* Les propriétés d’entités de type DateHeure sont toujours au format UTC.
* L’espace blanc dans la chaîne de requête doit être encodé en URL avant l’envoi d’une demande.

## <a name="comparison-operators"></a>Opérateurs de comparaison

Vous pouvez utiliser les opérateurs suivants pour comparer un champ à une valeur constante :

Opérateurs d’égalité :

- `eq`: teste si un champ est **égal à** une valeur constante
- `ne`: teste si un champ n’est **pas égal à** une valeur constante

Opérateurs de plage :

- `gt`: teste si un champ est **supérieur à** une valeur constante
- `lt`: teste si un champ est **inférieur à** une valeur constante
- `ge`: teste si un champ est **supérieur ou égal à** une valeur constante
- `le`: teste si un champ est **inférieur ou égal à** une valeur constante

## <a name="filter"></a>Filtrer

**$filter** - Utiliser le filtre pour fournir un paramètre de filtre OData afin de rechercher uniquement les objets qui vous intéressent.

L’exemple REST suivant filtre sur le paramètre alternateId d’une ressource :

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$filter=properties/alternateId%20eq%20'unique identifier'
```

L’exemple C# suivant filtre sur la date de création de la ressource :

```csharp
var odataQuery = new ODataQuery<Asset>("properties/created lt 2018-05-11T17:39:08.387Z");
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName, odataQuery);
```    

## <a name="order-by"></a>Trier par

**$orderby**  - À utiliser pour trier les objets retournés par le paramètre spécifié. Par exemple :    

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01$orderby=properties/created%20gt%202018-05-11T17:39:08.387Z
```

Pour trier les résultats par ordre croissant ou décroissant, ajoutez `asc` ou `desc` au nom du champ, en les séparant par un espace. Par exemple : `$orderby properties/created desc`.

## <a name="skip-token"></a>Jeton d’évitement

**$skiptoken** - Si une réponse de requête contient un grand nombre d’éléments, le service retourne une valeur de jeton d’évitement (`@odata.nextLink`) pour obtenir la page de résultats suivante. Celle-ci peut être utilisés pour parcourir le jeu de résultats entier.

Dans Media Services v3, vous ne pouvez pas configurer la taille de page. La taille de page varie selon le type d’entité. Pour plus d’informations, veuillez lire les sections suivantes.

Si des entités sont créées ou supprimées pendant la pagination de la collection, les changements sont reflétés dans les résultats retournés (si ces changements concernent la partie de la collection qui n’a pas été téléchargée). 

> [!TIP]
> Vous devez toujours utiliser `nextLink` pour énumérer la collection et ne pas dépendre d’une taille de page particulière.
>
> `nextLink` est présent uniquement s’il existe plus d’une page d’entités.

Prenons l’exemple suivant où $skiptoken est utilisé. Veillez à remplacer *amstestaccount* par le nom de votre compte et à définir la valeur *api-version* avec la version la plus récente.

Si vous demandez une liste d’actifs multimédias comme suit :

```
GET  https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01 HTTP/1.1
x-ms-client-request-id: dd57fe5d-f3be-4724-8553-4ceb1dbe5aab
Content-Type: application/json; charset=utf-8
```

Vous devez obtenir une réponse similaire à celle-ci :

```
HTTP/1.1 200 OK

{
"value":[
{
"name":"Asset 0","id":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 0","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-0000-0000-0000-000000000000","created":"2018-12-11T22:12:44.98Z","lastModified":"2018-12-11T22:15:48.003Z","container":"asset-00000000-0000-0000-0000-0000000000000","storageAccountName":"amsacctname","storageEncryptionFormat":"None"
}
},
// lots more assets
{
"name":"Asset 517","id":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 517","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-0000-0000-0000-000000000000","created":"2018-12-11T22:14:08.473Z","lastModified":"2018-12-11T22:19:29.657Z","container":"asset-00000000-0000-0000-0000-000000000000","storageAccountName":"amsacctname","storageEncryptionFormat":"None"
}
}
],"@odata.nextLink":"https:// management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517"
}
```

Vous devez ensuite demander la page suivante en envoyant une requête GET pour :

```
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517
```

L’exemple C# suivant montre comment énumérer tous les localisateurs de streaming dans le compte.

```csharp
var firstPage = await MediaServicesArmClient.StreamingLocators.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingLocators.ListNextAsync(currentPage.NextPageLink);
}
```

## <a name="using-logical-operators-to-combine-query-options"></a>Utilisation d’opérateurs logiques pour combiner des options de requête

Media Services v3 prend en charge les opérateurs logiques ’ou’ et ’et’. 

L’exemple REST suivant vérifie l’état de la tâche :

```
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/qbtest/providers/Microsoft.Media/mediaServices/qbtest/transforms/VideoAnalyzerTransform/jobs?$filter=properties/state%20eq%20Microsoft.Media.JobState'Scheduled'%20or%20properties/state%20eq%20Microsoft.Media.JobState'Processing'&api-version=2018-07-01
```

Vous construisez la même requête dans C# comme suit : 

```csharp
var odataQuery = new ODataQuery<Job>("properties/state eq Microsoft.Media.JobState'Scheduled' or properties/state eq Microsoft.Media.JobState'Processing'");
client.Jobs.List(config.ResourceGroup, config.AccountName, VideoAnalyzerTransformName, odataQuery);
```

## <a name="filtering-and-ordering-options-of-entities"></a>Filtrage et classement des options des entités

Le tableau suivant montre comment les options de filtrage et de classement peuvent être appliquées à des entités différentes :

|Nom de l’entité|Nom de la propriété|Filtrer|Ordre|
|---|---|---|---|
|[Éléments multimédias](https://docs.microsoft.com/rest/api/media/assets/)|Nom|`eq`, `gt`, `lt`, `ge`, `le`|`asc` et `desc`|
||properties.alternateId |`eq`||
||properties.assetId |`eq`||
||properties.created| `eq`, `gt`, `lt`| `asc` et `desc`|
|[Stratégies de clé de contenu](https://docs.microsoft.com/rest/api/media/contentkeypolicies)|Nom|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` et `desc`|
||properties.created    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` et `desc`|
||properties.description    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`||
||properties.lastModified|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` et `desc`|
||properties.policyId|`eq`, `ne`||
|[Tâches](https://docs.microsoft.com/rest/api/media/jobs)| Nom  | `eq`            | `asc` et `desc`|
||properties.state        | `eq`, `ne`        |                         |
||properties.created      | `gt`, `ge`, `lt`, `le`| `asc` et `desc`|
||properties.lastModified | `gt`, `ge`, `lt`, `le` | `asc` et `desc`| 
|[Localisateurs de Streaming](https://docs.microsoft.com/rest/api/media/streaminglocators)|Nom|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` et `desc`|
||properties.created    |`eq`, `ne`, `ge`, `le`,  `gt`, `lt`|`asc` et `desc`|
||properties.endTime    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` et `desc`|
|[Stratégies de streaming](https://docs.microsoft.com/rest/api/media/streamingpolicies)|Nom|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` et `desc`|
||properties.created    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` et `desc`|
|[Transformations](https://docs.microsoft.com/rest/api/media/transforms)| Nom | `eq`            | `asc` et `desc`|
|| properties.created      | `gt`, `ge`, `lt`, `le`| `asc` et `desc`|
|| properties.lastModified | `gt`, `ge`, `lt`, `le`| `asc` et `desc`|

## <a name="next-steps"></a>Étapes suivantes

* [Répertorier les ressources](https://docs.microsoft.com/rest/api/media/assets/list)
* [Répertorier les stratégies de clé de contenu](https://docs.microsoft.com/rest/api/media/contentkeypolicies/list)
* [Répertorier les travaux](https://docs.microsoft.com/rest/api/media/jobs/list)
* [Répertorier les stratégies de streaming](https://docs.microsoft.com/rest/api/media/streamingpolicies/list)
* [Répertorier les localisateurs de streaming](https://docs.microsoft.com/rest/api/media/streaminglocators/list)
* [Diffuser un fichier](stream-files-dotnet-quickstart.md)
* [Quotas et limitations](limits-quotas-constraints.md)
