---
title: Présentation des unités réservées Multimédia | Microsoft Docs
description: Cet article présente la mise à l'échelle du traitement multimédia à l'aide d'Azure Media Services.
services: media-services
documentationcenter: ''
author: jiayali
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/24/2021
ms.author: inhenkel
ms.openlocfilehash: 19454d90b55ab92c92d2e9132198f918f38c6e2c
ms.sourcegitcommit: d858083348844b7cf854b1a0f01e3a2583809649
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122835381"
---
# <a name="media-reserved-units"></a>Unités réservées Multimédia

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Les unités réservées Multimédia (MRU) ont été précédemment utilisées pour contrôler l’accès concurrentiel et les performances d’encodage. Les unités réservées Multimédia sont utilisées uniquement pour les processeurs multimédia hérités suivants qui seront bientôt obsolètes. Consultez [Composants Azure Media Services hérités](legacy-components.md) pour obtenir des informations sur la mise hors service pour ces processeurs hérités :

* Media Encoder Premium Workflow
* Media Indexer V1 et V2

Pour tous les autres processeurs multimédia, vous n’avez plus besoin de gérer des unités réservées Multimédia ou de demander des augmentations de quota pour un compte Media Services, car le système est automatiquement mis à l’échelle en fonction de la charge. Vous verrez également les performances qui sont égales ou améliorées par rapport à l’utilisation d’unités réservées Multimédia.

## <a name="billing"></a>Facturation

Bien qu’il y ait eu précédemment des frais pour les unités réservées Multimédia, à compter du 17 avril, 2021 il n’y a plus de frais pour les comptes qui ont une configuration pour les unités réservées Multimédia.

## <a name="scaling-mrus"></a>Mise à l’échelle des unités réservées Multimédia

Pour des raisons de compatibilité, vous pouvez continuer à utiliser la Portail Azure ou les API suivantes pour gérer et mettre à l’échelle les unités réservées Multimédia :

[.NET](media-services-dotnet-encoding-units.md)
[Portal](media-services-portal-scale-media-processing.md)
[REST](/rest/api/media/operations/encodingreservedunittype)
[Java](https://github.com/rnrneverdies/azure-sdk-for-media-services-java-samples)
[PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

Toutefois, par défaut, aucune des dernières configurations que vous avez définies ne sera utilisée pour contrôler l’accès concurrentiel ou les performances de l’encodage. La seule exception à cette configuration a lieu si vous encodez avec l’un des processeurs multimédias hérités suivants : Media Encoder Premium Workflow ou Media Indexer V1.  
