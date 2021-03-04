---
title: Écarts de fonctionnalités entre Azure Media Services v2 et v3
description: Cet article décrit les écarts de fonctionnalités entre Azure Media Services v2 et v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: f77a41a172bc868ad056d74d018b50f734286f0f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101702451"
---
# <a name="feature-gaps-between-azure-media-services-v2-and-v3"></a>Écarts de fonctionnalités entre Azure Media Services v2 et v3

![logo du guide de migration](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![étapes de migration 2](./media/migration-guide/steps-2.svg)

Cette partie de l’aide à la migration vous donne des informations détaillées sur les différences entre les API V2 et V3.

## <a name="feature-gaps-between-v2-and-v3-apis"></a>Écarts de fonctionnalités entre les API V2 et V3

L’API V3 présente les écarts de fonctionnalités suivants par rapport à l’API V2. Certaines fonctionnalités avancées de Media Encoder Standard dans les API V2 ne sont actuellement pas disponibles dans V3 :

- Insertion d’une piste audio en mode silencieux quand l’entrée ne produit pas de son, car cela n’est plus nécessaire avec le lecteur multimédia Azure.

- Insertion d’une piste vidéo quand l’entrée ne comporte aucune vidéo.

- Les événements en direct avec transcodage ne prennent actuellement pas en charge l’insertion d’ardoise à mi-parcours ni l’insertion de marqueur publicitaire par le biais d’un appel d’API.

- Azure Media Premium Encoder n’est plus pris en charge dans V2. Si vous l’utilisez pour l’encodage HEVC 8 bits, utilisez la nouvelle prise en charge de HEVC dans l’encodeur Standard. 
    - Nous avons ajouté la prise en charge du mappage de canal audio à l’encodeur Standard.  Consultez [Audio dans la documentation relative au Swagger d’encodage de Media Services](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json).
    - Si vous utilisiez des fonctionnalités avancées ou des formats de sortie de produits sous licence tiers, comme MXF ou ProRes, utilisez la solution Azure Partner de Telestream, qui sera transactionnelle d’ici la mise hors service de V2. Vous pouvez également utiliser Imagine Communications ou [Bitmovin](http://bitmovin.com).

- La propriété « groupe à haute disponibilité » sur le point de terminaison de streaming dans V2 n’est plus prise en charge. Consultez l’exemple de projet et les conseils en matière de livraison de [VOD haute disponibilité](./media-services-high-availability-encoding.md) dans l’API V3.

- Dans Media Services V3, vous ne pouvez pas spécifier le système FairPlay IV. Bien qu’il n’ait pas d’impact sur les clients qui utilisent Media Services pour l’empaquetage et la remise de licence, cela peut être problématique lors de l’utilisation d’un système DRM tiers pour fournir les licences FairPlay (mode hybride).

- Le chiffrement du stockage côté client pour la protection des ressources au repos a été supprimé de l’API V3 et remplacé par Storage Service Encryption pour les données au repos. Les API V3 continuent de fonctionner avec les ressources chiffrées du stockage existant mais elles n’autoriseront pas de nouvelles créations.

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]