---
title: Localisateurs de diffusion en continu dans Azure Media Services | Microsoft Docs
description: Cet article explique ce que sont les localisateurs de diffusion en continu et comment Azure Media Services les utilise.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 12/20/2018
ms.author: juliako
ms.openlocfilehash: 658843fd5acbe0d4e29947e99c00edf4909fe9f4
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53742743"
---
# <a name="streaming-locators"></a>Localisateurs de diffusion en continu

Vous devez fournir à vos clients une URL qu'ils pourront utiliser pour lire les fichiers audio ou vidéo encodés. Vous devez également créer un [localisateur de diffusion en continu](https://docs.microsoft.com/rest/api/media/streaminglocators) et générer les URL de diffusion en continu. Pour plus d’informations, consultez la page [sur la diffusion en continu des fichiers](stream-files-dotnet-quickstart.md).

## <a name="streaminglocator-definition"></a>Définition d’un localisateur de diffusion en continu

Le tableau suivant présente les propriétés d’un localisateur de diffusion en continu et en donne la définition.

|NOM|Description|
|---|---|
|id |ID de ressource complet pour la ressource.|
|Nom|Nom de la ressource.|
|properties.alternativeMediaId|Autre ID de support de ce localisateur de diffusion en continu.|
|properties.assetName|Nom de la ressource|
|properties.contentKeys|Les ContentKeys utilisées par ce localisateur de diffusion en continu.|
|properties.created|L’heure de création du localisateur de diffusion en continu.|
|properties.defaultContentKeyPolicyName|Nom de la valeur ContentKeyPolicy par défaut utilisée par ce localisateur de diffusion en continu.|
|properties.endTime|Heure de fin du localisateur de diffusion en continu.|
|properties.startTime|Heure de début du localisateur de diffusion en continu.|
|properties.streamingLocatorId|ID du localisateur de diffusion en continu.|
|properties.streamingPolicyName |Nom de la stratégie de diffusion en continu utilisée par ce localisateur de diffusion en continu. Spécifiez le nom de la stratégie de streaming que vous avez créée ou utilisez l’une des stratégies de streaming prédéfinies. Voici les stratégies de diffusion en continu prédéfinies disponibles : 'Predefined_DownloadOnly', 'Predefined_ClearStreamingOnly', 'Predefined_DownloadAndClearStreaming', 'Predefined_ClearKey', 'Predefined_MultiDrmCencStreaming' et 'Predefined_MultiDrmStreaming'|
|Type|Type de la ressource.|

Pour obtenir la définition complète, consultez [Streaming Locators](https://docs.microsoft.com/rest/api/media/streaminglocators) (Localisateurs de diffusion en continu).

## <a name="filtering-ordering-paging"></a>Filtrage, tri, pagination

Media Services prend en charge les options de requête OData suivantes pour les localisateurs de diffusion en continu : 

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

Le tableau suivant montre comment ces options peuvent être appliquées aux propriétés du localisateur de diffusion en continu : 

|NOM|Filtrer|Ordre|
|---|---|---|
|id |||
|Nom|Eq, ne, ge, le, gt, lt|Croissant et décroissant|
|properties.alternativeMediaId  |||
|properties.assetName   |||
|properties.contentKeys |||
|properties.created |Eq, ne, ge, le, gt, lt|Croissant et décroissant|
|properties.defaultContentKeyPolicyName |||
|properties.endTime |Eq, ne, ge, le, gt, lt|Croissant et décroissant|
|properties.startTime   |||
|properties.streamingLocatorId  |||
|properties.streamingPolicyName |||
|Type   |||

### <a name="pagination"></a>Pagination

La pagination est prise en charge pour chacun des quatre ordres de tri activés. Actuellement, la taille de page est de 10.

> [!TIP]
> Vous devez toujours utiliser le lien suivant pour énumérer la collection et ne pas dépendre d’une taille de page particulière.

Si une réponse de requête contient un grand nombre d’éléments, le service retourne une propriété « \@odata.nextLink » pour obtenir la page de résultats suivante. Celle-ci peut être utilisés pour parcourir le jeu de résultats entier. Vous ne pouvez pas configurer la taille de page. 

Si des localisateurs de diffusion en continu sont créés ou supprimés pendant la pagination de la collection, les changements sont répercutés dans les résultats retournés (si ces changements concernent la partie de la collection qui n’a pas été téléchargée). 

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

## <a name="next-steps"></a>Étapes suivantes

[Diffuser un fichier](stream-files-dotnet-quickstart.md)
