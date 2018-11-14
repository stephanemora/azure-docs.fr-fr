---
title: Azure Media Services v3 - Forum aux questions | Microsoft Docs
description: Cet article donne des réponses aux questions les plus fréquentes sur Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 11/05/2018
ms.author: juliako
ms.openlocfilehash: 26661a213b5df5424bf9ab9ad799345ae35620ea
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2018
ms.locfileid: "51036765"
---
# <a name="azure-media-services-v3-frequently-asked-questions"></a>Forum Aux Questions (FAQ) Azure Media Services v3

Cet article donne des réponses aux questions les plus fréquentes sur Azure Media Services (AMS) v3.

## <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Puis-je utiliser le portail Azure pour gérer des ressources v3 ?

Pas encore. Vous pouvez utiliser un des SDK pris en charge. Consultez les tutoriels et les exemples de cet ensemble de documentations.

## <a name="is-there-an-api-for-configuring-media-reserved-units"></a>Existe-t-il une API pour configurer les unités réservées Multimédia ?

Actuellement, vous devez utiliser les API AMS v2 pour configurer des unités réservées Multimédia (comme décrit dans [Mise à l’échelle du traitement multimédia](../previous/media-services-scale-media-processing-overview.md). 

Lorsque vous utilisez **VideoAnalyzerPreset** et/ou **AudioAnalyzerPreset**, définissez votre compte Media Services à 10 unités réservées Multimédia S3.

## <a name="does-v3-asset-have-no-assetfile-concept"></a>Une ressource V3 n’a-t-elle pas de concept de fichier d’élément multimédia ?

Les fichiers d’élément multimédia ont été supprimés de l’API AMS afin de séparer Media Services de la dépendance du SDK de Stockage. C’est maintenant Stockage, et non plus Media Services, qui conserve les informations appartenant à Stockage. 

## <a name="where-did-client-side-storage-encryption-go"></a>Où le chiffrement du stockage côté client se fait-il ?

Nous vous recommandons maintenant le chiffrement du stockage côté serveur (qui est activé par défaut). Pour plus d’informations, consultez [Chiffrement du service Stockage Azure pour les données au repos](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

## <a name="what-is-the-recommended-upload-method"></a>Quelle est la méthode de chargement recommandée ?

Nous recommandons d’utiliser des ingestions HTTP(s). Pour plus d’informations, consultez [Ingestions HTTP(s)](job-input-from-http-how-to.md).

## <a name="how-does-pagination-work"></a>Comment fonctionne la pagination ?

Media Services prend en charge $top pour les ressources qui prennent en charge OData, mais la valeur passée à $top doit être inférieur à 1 000 (par exemple, la taille de page pour la pagination).

Ceci vous permet d’obtenir un petit échantillon d’éléments en utilisant $top (par exemple les 100 éléments les plus récents) ou de paginer dans tous les éléments à l’aide de la pagination. 

Media Services ne prend pas en charge la pagination dans des données avec une taille de page spécifiée par l’utilisateur.

Pour plus d’informations, consultez [Filtrage, tri, pagination](assets-concept.md#filtering-ordering-paging)

## <a name="how-to-retrieve-an-entity-in-media-services-v3"></a>Comment récupérer une entité dans Media Services v3 ?

v3 est basée sur une surface d’API unifiée qui expose les fonctionnalités de gestion et de fonctionnement reposant sur **Azure Resource Manager**. Conformément à **Azure Resource Manager**, les noms des ressources sont toujours uniques. Ainsi, vous pouvez utiliser n’importe quelle chaîne d’identificateur unique (par exemple des GUID) pour les noms de vos ressources. 

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Vue d’ensemble de Media Services v3](media-services-overview.md)
