---
title: Stratégies de clé de contenu dans Media Services - Azure | Microsoft Docs
description: Cet article explique ce que sont les stratégies de clé de contenu et comment Azure Media Services les utilise.
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
ms.custom: seodec18
ms.openlocfilehash: f12632b20d516c81e21a50cfdda7e40d4163afc1
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53742216"
---
# <a name="content-key-policies"></a>Stratégies de clé de contenu

Vous pouvez utiliser Azure Media Services pour sécuriser votre contenu multimédia du moment où il quitte votre ordinateur jusqu’à la remise, en passant par le stockage et le traitement. Media Services vous permet de transmettre votre contenu dynamique ou à la demande chiffré dynamiquement avec la norme Advanced Encryption Standard (AES-128) ou un des principaux systèmes de gestion des droits numériques (DRM) : Microsoft PlayReady, Google Widevine et Apple FairPlay. Media Services fournit également un service de distribution de clés AES et licences (PlayReady, Widevine et FairPlay) DRM aux clients autorisés.

Dans Azure Media Services v3, une [stratégie de clé de contenu](https://docs.microsoft.com/rest/api/media/contentkeypolicies) vous permet de spécifier la façon dont la clé de contenu est remise aux clients finaux via le composant de remise de clés Media Services. Pour plus d’informations, consultez [Présentation de la protection du contenu](content-protection-overview.md).

Il est recommandé de réutiliser la même stratégie ContentKeyPolicy pour toutes vos ressources. Les stratégies ContentKeyPolicies peuvent être mises à jour ; si vous voulez effectuer une rotation des clés, vous pouvez ajouter une nouvelle stratégie ContentKeyPolicyOption à la stratégie ContentKeyPolicy existante avec une restriction par jeton avec les nouvelles clés. Vous pouvez également mettre à jour la clé de vérification principale et la liste des clés de vérification alternatives dans la stratégie et l’option existantes. Cela peut prendre jusqu’à 15 minutes pour que les caches de livraison de clés mettent à jour et récupèrent la stratégie mise à jour.

## <a name="contentkeypolicy-definition"></a>Définition de stratégie ContentKeyPolicy

Le tableau suivant présente les propriétés d’une stratégie ContentKeyPolicy et en donne la définition.

|NOM|Description|
|---|---|
|id|ID de ressource complet pour la ressource.|
|Nom|Nom de la ressource.|
|properties.created |Date de création de la stratégie.|
|properties.description |Description de la stratégie.|
|properties.lastModified|Date de la dernière modification de la stratégie.|
|properties.options |Options de la stratégie de clé.|
|properties.policyId|ID de stratégie hérité.|
|Type|Type de la ressource.|

Pour obtenir la définition complète, consultez [Stratégies de clé de contenu](https://docs.microsoft.com/rest/api/media/contentkeypolicies).

## <a name="filtering-ordering-paging"></a>Filtrage, tri, pagination

Media Services prend en charge les options de requête OData suivantes pour les stratégies ContentKeyPolicy : 

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

Le tableau suivant montre comment ces options peuvent être appliquées aux propriétés de ContentKeyPolicies : 

|NOM|Filtrer|Ordre|
|---|---|---|
|id|||
|Nom|Eq, ne, ge, le, gt, lt|Croissant et décroissant|
|properties.created |Eq, ne, ge, le, gt, lt|Croissant et décroissant|
|properties.description |Eq, ne, ge, le, gt, lt||
|properties.lastModified|Eq, ne, ge, le, gt, lt|Croissant et décroissant|
|properties.options |||
|properties.policyId|Eq, ne||
|Type|||

### <a name="pagination"></a>Pagination

La pagination est prise en charge pour chacun des quatre ordres de tri activés. Actuellement, la taille de page est de 10.

> [!TIP]
> Vous devez toujours utiliser le lien suivant pour énumérer la collection et ne pas dépendre d’une taille de page particulière.

Si une réponse de requête contient un grand nombre d’éléments, le service retourne une propriété « \@odata.nextLink » pour obtenir la page de résultats suivante. Celle-ci peut être utilisés pour parcourir le jeu de résultats entier. Vous ne pouvez pas configurer la taille de page. 

Si des stratégies ContentKeyPolicies sont créées ou supprimées pendant la pagination dans la collection, les changements sont reflétés dans les résultats retournés (si ces changements concernent la partie de la collection qui n’a pas été téléchargée) 

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
