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
ms.date: 02/05/2019
ms.author: juliako
ms.openlocfilehash: be4c08bc31c8811655230ab89b48271f4c2b3164
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/06/2019
ms.locfileid: "55756578"
---
# <a name="azure-media-services-v3-frequently-asked-questions"></a>Forum Aux Questions (FAQ) Azure Media Services v3

Cet article donne des réponses aux questions les plus fréquentes sur Azure Media Services (AMS) v3.

## <a name="v3-apis"></a>API v3

### <a name="how-do-i-configure-media-reserved-units"></a>Comment configurer des unités réservées Multimédia ?

Pour les travaux d’analyse audio et vidéo déclenchés par Media Services v3 ou Video Indexer, nous vous recommandons de provisionner votre compte avec des MRU 10 S3. Si vous avez besoin de plus de 10 MRU S3, ouvrez un ticket de support à l’aide du [Portail Azure](https://portal.azure.com/).

Pour plus de détails, voir [Mise à l’échelle du traitement multimédia avec l’interface CLI](media-reserved-units-cli-how-to.md).

### <a name="what-is-the-recommended-method-to-process-videos"></a>Quelle est la méthode recommandée pour traiter les vidéos ?

Nous vous recommandons de soumettre des travaux en utilisant une URL HTTP(s) pointant vers la vidéo. Pour plus d’informations, consultez [Ingestions HTTP(s)](job-input-from-http-how-to.md). Vous n’êtes pas obligé de créer une ressource avec la vidéo d’entrée pour que celle-ci puisse être traitée.

### <a name="how-does-pagination-work"></a>Comment fonctionne la pagination ?

Media Services prend en charge $top pour les ressources qui prennent en charge OData, mais la valeur passée à $top doit être inférieur à 1 000 (par exemple, la taille de page pour la pagination).

Ceci vous permet d’obtenir un petit échantillon d’éléments en utilisant $top (par exemple les 100 éléments les plus récents) ou de paginer dans tous les éléments à l’aide de la pagination. 

Media Services ne prend pas en charge la pagination dans des données avec une taille de page spécifiée par l’utilisateur.

Pour plus d’informations, consultez [Filtrage, tri, pagination](entities-overview.md).

### <a name="how-to-retrieve-an-entity-in-media-services-v3"></a>Comment récupérer une entité dans Media Services v3 ?

v3 est basée sur une surface d’API unifiée qui expose les fonctionnalités de gestion et de fonctionnement reposant sur **Azure Resource Manager**. Conformément à **Azure Resource Manager**, les noms des ressources sont toujours uniques. Ainsi, vous pouvez utiliser n’importe quelle chaîne d’identificateur unique (par exemple des GUID) pour vos ressources.

## <a name="live-streaming"></a>Vidéo en flux continu 

###  <a name="how-to-insert-breaksvideos-and-image-slates-during-live-stream"></a>Comment insérer des pauses/séquences d’images et de vidéos pendant le flux de données en direct ?

Le Live Encoding Media Services v3 ne prend pas encore en charge l’insertion de séquences de vidéos ou d’images pendant le flux de données en direct. 

Vous pouvez utiliser un [encodeur live local](recommended-on-premises-live-encoders.md) pour changer la vidéo source. De nombreuses applications offrent la possibilité de changer les sources, parmi lesquelles Telestream Wirecast, Switcher Studio (sur iOS), OBS Studio (application gratuite).

## <a name="media-services-v2-vs-v3"></a>Media Services v2 et v3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Puis-je utiliser le portail Azure pour gérer des ressources v3 ?

Pas encore. Vous pouvez utiliser un des SDK pris en charge. Consultez les tutoriels et les exemples de cet ensemble de documentations.

### <a name="is-there-an-assetfile-concept-in-v3"></a>Existe-t-il un concept de fichier d’élément multimédia dans v3 ?

Les fichiers d’élément multimédia ont été supprimés de l’API AMS afin de séparer Media Services de la dépendance du SDK de Stockage. C’est maintenant Stockage, et non plus Media Services, qui conserve les informations appartenant à Stockage. 

Pour plus d’informations, consultez [Migrer vers Azure Media Services v3](migrate-from-v2-to-v3.md).

### <a name="where-did-client-side-storage-encryption-go"></a>Où le chiffrement du stockage côté client se fait-il ?

Il est désormais recommandé d’utiliser le chiffrement de stockage côté serveur (qui est activé par défaut). Pour plus d’informations, consultez [Azure Storage Service Encryption pour les données au repos](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Vue d’ensemble de Media Services v3](media-services-overview.md)
