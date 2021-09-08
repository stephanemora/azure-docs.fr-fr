---
title: Mettre à l’échelle le traitement multimédia à l’aide du portail Azure | Microsoft Docs
description: Ce didacticiel vous guide à travers les étapes de mise à l’échelle du traitement multimédia à l’aide du portail Azure.
services: media-services
documentationcenter: ''
author: jiayali
manager: femila
editor: ''
ms.assetid: e500f733-68aa-450c-b212-cf717c0d15da
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/24/2021
ms.author: inhenkel
ms.openlocfilehash: 1040ffa8bc3af3734a5c37961676b0914f72e991
ms.sourcegitcommit: d858083348844b7cf854b1a0f01e3a2583809649
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122835870"
---
# <a name="change-the-reserved-unit-type"></a>Modification du type d’unité réservée

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encoding-units.md)
> * [Portail](media-services-portal-scale-media-processing.md)
> * [REST](/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/rnrneverdies/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

## <a name="overview"></a>Vue d’ensemble

Par défaut, les unités réservées de support ne doivent plus nécessairement être utilisées et ne sont pas prises en charge par Azure Media Services. Pour des raisons de compatibilité, le portail Azure actuel offre une option vous permettant de gérer et de mettre à l’échelle les MRU. Toutefois, par défaut, aucune des configurations de MRU que vous avez définies ne sera utilisée pour contrôler l’accès concurrentiel ou les performances de l’encodage.

> [!IMPORTANT]
> Pour obtenir plus d’informations sur la mise à l’échelle du traitement multimédia, consultez la rubrique de [présentation](media-services-scale-media-processing-overview.md) .

## <a name="scale-media-processing"></a>Mise à l’échelle du traitement multimédia
>[!NOTE]
>La sélection de MRU n’affecte pas la concurrence ou les performances dans Azure Media Services V3. 

Pour modifier le type d’unité réservée et le nombre d’unités réservées, procédez comme suit :

1. Dans le [portail Azure](https://portal.azure.com/), sélectionnez votre compte Azure Media Services.
2. Dans la fenêtre **Paramètres**, sélectionnez **Unités réservées Multimédia**.
   
    Pour modifier le nombre d’unités réservées pour le type d’unité réservé sélectionné, utilisez le curseur **Unités réservées multimédia** en haut de l’écran.
   
    Pour modifier le **TYPE D'UNITÉ RÉSERVÉ**, cliquez sur la barre **Vitesse des unités de traitement réservées**. Ensuite, sélectionnez le niveau de tarification dont vous avez besoin : S1, S2 ou S3.
   
3. Appuyez sur le bouton ENREGISTRER pour enregistrer vos modifications.
   
    Les nouvelles unités réservées sont allouées lorsque vous cliquez sur ENREGISTRER.

## <a name="next-steps"></a>Étapes suivantes
Consultez les parcours d’apprentissage de Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fournir des commentaires
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
