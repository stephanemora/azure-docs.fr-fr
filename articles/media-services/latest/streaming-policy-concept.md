---
title: Stratégies de streaming dans Azure Media Services | Microsoft Docs
description: Cet article explique ce que sont les stratégies de streaming et comment Azure Media Services les utilise.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/16/2018
ms.author: juliako
ms.openlocfilehash: 4d02ddf50660cd700b2f1c5999ceadfb472b6906
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49380586"
---
# <a name="streaming-policies"></a>Stratégies de streaming

Dans Azure Media Services v3, les stratégies de streaming vous permettent de définir les protocoles de streaming et les options de chiffrement pour vos StreamingLocators. Vous pouvez spécifier le nom de la stratégie de streaming que vous avez créée ou utiliser l’une des stratégies de streaming prédéfinies. Les stratégies de streaming prédéfinies actuellement disponibles sont les suivantes : « Predefined_DownloadOnly », « Predefined_ClearStreamingOnly », « Predefined_DownloadAndClearStreaming », « Predefined_ClearKey », 'Predefined_MultiDrmCencStreaming » et « Predefined_ MultiDrmStreaming ».

> [!IMPORTANT]
> Lorsque vous utilisez une stratégie [StreamingPolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies) personnalisée, vous devez concevoir un ensemble limité de ces stratégies pour votre compte Media Services et les réutiliser pour vos éléments StreamingLocators chaque fois que les mêmes protocoles et options de chiffrement sont nécessaires. Votre compte Media Services a un quota en matière de nombre d’entrées de stratégie StreamingPolicy. Vous ne devez pas créer une stratégie StreamingPolicy pour chaque élément StreamingLocator.

## <a name="streamingpolicy-definition"></a>Définition d’une stratégie StreamingPolicy

Le tableau suivant présente les propriétés d’une stratégie StreamingPolicy et en donne la définition.

|Nom|Type|Description|
|---|---|---|
|id|chaîne|ID de ressource complet pour la ressource.|
|Nom|chaîne|Nom de la ressource.|
|properties.commonEncryptionCbcs|CommonEncryptionCbcs|Configuration de CommonEncryptionCbcs|
|properties.commonEncryptionCenc|CommonEncryptionCenc|Configuration de CommonEncryptionCenc|
|properties.created |chaîne|Heure de création de la stratégie de streaming|
|properties.defaultContentKeyPolicyName |chaîne|Clé de contenu (ContentKey) par défaut utilisée par la stratégie de streaming active|
|properties.envelopeEncryption  |EnvelopeEncryption|Configuration de EnvelopeEncryption|
|properties.noEncryption|NoEncryption|Configurations de NoEncryption|
|type|chaîne|Type de la ressource.|

Pour obtenir la définition complète, consultez [Stratégies de streaming](https://docs.microsoft.com/rest/api/media/streamingpolicies).

## <a name="filtering-ordering-paging"></a>Filtrage, tri, pagination

Media Services prend en charge les options de requête OData suivantes pour les stratégies de streaming : 

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

Le tableau suivant montre comment ces options peuvent être appliquées aux propriétés de StreamingPolicy : 

|Nom|Filtre|Ordre|
|---|---|---|
|id|||
|Nom|Eq, ne, ge, le, gt, lt|Croissant et décroissant|
|properties.commonEncryptionCbcs|||
|properties.commonEncryptionCenc|||
|properties.created |Eq, ne, ge, le, gt, lt|Croissant et décroissant|
|properties.defaultContentKeyPolicyName |||
|properties.envelopeEncryption|||
|properties.noEncryption|||
|type|||

### <a name="pagination"></a>Pagination

La pagination est prise en charge pour chacun des quatre ordres de tri activés. Actuellement, la taille de page est de 10.

> [!TIP]
> Vous devez toujours utiliser le lien suivant pour énumérer la collection et ne pas dépendre d’une taille de page particulière.

Si une réponse de requête contient un grand nombre d’éléments, le service retourne une propriété « \@odata.nextLink » pour obtenir la page de résultats suivante. Celle-ci peut être utilisés pour parcourir le jeu de résultats entier. Vous ne pouvez pas configurer la taille de page. 

Si des stratégies StreamingPolicy sont créées ou supprimées pendant la pagination de la collection, les changements sont répercutés dans les résultats retournés (si ces changements concernent la partie de la collection qui n’a pas été téléchargée). 

L’exemple C# suivant montre comment énumérer toutes les stratégies StreamingPolicy dans le compte.

```csharp
var firstPage = await MediaServicesArmClient.StreamingPolicies.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingPolicies.ListNextAsync(currentPage.NextPageLink);
}
```

Pour obtenir des exemples REST, consultez [Stratégies de streaming - Liste](https://docs.microsoft.com/rest/api/media/streamingpolicies/streamingpolicies_list)

## <a name="next-steps"></a>Étapes suivantes

[Diffuser un fichier](stream-files-dotnet-quickstart.md)
