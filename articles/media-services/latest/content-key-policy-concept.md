---
title: Stratégies de clé de contenu dans Azure Media Services | Microsoft Docs
description: Cet article explique ce que sont les stratégies de clé de contenu et comment Azure Media Services les utilise.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/22/2018
ms.author: juliako
ms.openlocfilehash: 9a5ef8df9b1ca87430fb5e8d1da94f1899c4a856
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49985860"
---
# <a name="content-key-policies"></a>Stratégies de clé de contenu

Vous pouvez utiliser Azure Media Services pour sécuriser votre contenu multimédia du moment où il quitte votre ordinateur jusqu’à la remise, en passant par le stockage et le traitement. Media Services vous permet de transmettre votre contenu dynamique ou à la demande chiffré dynamiquement avec la norme Advanced Encryption Standard (AES-128) ou un des principaux systèmes de gestion des droits numériques (DRM) : Microsoft PlayReady, Google Widevine et Apple FairPlay. Media Services fournit également un service de distribution de clés AES et licences (PlayReady, Widevine et FairPlay) DRM aux clients autorisés.

Dans Azure Media Services v3, les stratégies de clé de contenu vous permettent de spécifier la façon dont la clé de contenu est remise aux clients finaux via le composant de remise de clés Media Services. Pour plus d’informations, consultez [Présentation de la protection du contenu](content-protection-overview.md).

## <a name="contentkeypolicies-definition"></a>Définition de stratégies ContentKeyPolicy

Le tableau suivant présente les propriétés d’une stratégie ContentKeyPolicy et en donne la définition.

|NOM|type|Description|
|---|---|---|
|id|chaîne|ID de ressource complet pour la ressource.|
|Nom|chaîne|Nom de la ressource.|
|properties.created |chaîne|Date de création de la stratégie.|
|properties.description |chaîne|Description de la stratégie.|
|properties.lastModified    |chaîne|Date de la dernière modification de la stratégie.|
|properties.options |ContentKeyPolicyOption[]|Options de la stratégie de clé.|
|properties.policyId    |chaîne|ID de stratégie hérité.|
|Type   |chaîne|Type de la ressource.|

Pour obtenir la définition complète, consultez [Stratégies de clé de contenu](https://docs.microsoft.com/rest/api/media/contentkeypolicies).

## <a name="filtering-ordering-paging"></a>Filtrage, tri, pagination

Media Services prend en charge les options de requête OData suivantes pour les stratégies ContentKeyPolicy : 

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

|NOM|Filtrer|Ordre|
|---|---|---|
|id|||
|Nom|Eq, ne, ge, le, gt, lt|Croissant et décroissant|
|properties.created |Eq, ne, ge, le, gt, lt|Croissant et décroissant|
|properties.description |Eq, ne, ge, le, gt, lt||
|properties.lastModified    |Eq, ne, ge, le, gt, lt|Croissant et décroissant|
|properties.options |||
|properties.policyId    |Eq, ne||
|Type   |||

### <a name="pagination"></a>Pagination

La pagination est prise en charge pour chacun des quatre ordres de tri activés. Actuellement, la taille de page est de 10.

> [!TIP]
> Vous devez toujours utiliser le lien suivant pour énumérer la collection et ne pas dépendre d’une taille de page particulière.

Si une réponse de requête contient un grand nombre d’éléments, le service retourne une propriété « \@odata.nextLink » pour obtenir la page de résultats suivante. Celle-ci peut être utilisés pour parcourir le jeu de résultats entier. Vous ne pouvez pas configurer la taille de page. 

Si des stratégies StreamingPolicy sont créées ou supprimées pendant la pagination de la collection, les changements sont répercutés dans les résultats retournés (si ces changements concernent la partie de la collection qui n’a pas été téléchargée). 

L’exemple C# suivant montre comment énumérer toutes les stratégies ContentKeyPolicy dans le compte.

```csharp
var firstPage = await MediaServicesArmClient.ContentKeyPolicies.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.ContentKeyPolicies.ListNextAsync(currentPage.NextPageLink);
}
```

Pour obtenir des exemples REST, consultez [Stratégies de clé de contenu - Liste](https://docs.microsoft.com/rest/api/media/contentkeypolicies/list)

## <a name="next-steps"></a>Étapes suivantes

[Utiliser le chiffrement dynamique AES-128 et le service de distribution des clés](protect-with-aes128.md)

[Utilisation du chiffrement dynamique DRM et du service de remise des licences](protect-with-drm.md)
