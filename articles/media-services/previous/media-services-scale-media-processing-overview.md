---
title: Vue d’ensemble de la mise à l’échelle du traitement multimédia | Microsoft Docs
description: Cette rubrique est une présentation de la mise à l’échelle du traitement multimédia avec Azure Media Services.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/26/2019
ms.author: juliako
ms.openlocfilehash: 1d2ef02ea77ad2bca37f1e397b784d06481538fa
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89264009"
---
# <a name="scaling-media-processing-overview"></a>Vue d’ensemble de la mise à l’échelle du traitement multimédia 

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Cette page fournit une vue d’ensemble de la procédure de mise à l’échelle du traitement multimédia et de ses avantages. 

## <a name="overview"></a>Vue d’ensemble
Un compte Media Services est associé à un Type d'unité réservé qui détermine la vitesse à laquelle vos tâches de traitement multimédia sont traitées. Vous pouvez choisir entre les types d’unités réservées suivantes : **S1**, **S2** ou **S3**. Par exemple, un même travail d’encodage s’exécute plus rapidement quand vous utilisez le type d’unité réservée **S2** que le type **S1**. Pour plus d’informations, consultez [Types d’unités réservées](https://azure.microsoft.com/blog/high-speed-encoding-with-azure-media-services/).

En plus de spécifier le type d’unité réservée, vous pouvez spécifier d’approvisionner votre compte avec des unités réservées. Le nombre d’unités réservées approvisionnées détermine le nombre de tâches multimédias qui peuvent être traitées simultanément dans un compte donné. Si, par exemple, votre compte a cinq unités réservées, les cinq tâches multimédias sont exécutées simultanément tant qu’il y a des tâches à traiter. Les autres tâches restent dans la file d'attente et sont sélectionnées séquentiellement pour le traitement quand l'exécution d'une tâche se termine. Si aucune unité réservée n'est approvisionnée pour un compte donné, les tâches sont sélectionnées séquentiellement. Dans ce cas, le temps d'attente entre l'achèvement d'une tâche et le démarrage de la suivante dépend de la disponibilité des ressources dans le système.

## <a name="choosing-between-different-reserved-unit-types"></a>Choix entre les différents types d’unités réservées
Le tableau suivant vous aide à choisir entre les différentes vitesses d’encodage. Il fournit également quelques cas de test d’évaluation sur [une vidéo que vous pouvez télécharger](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z) pour effectuer vos propres tests :

|Type de RU|Scénario|Exemple de résultats pour la [vidéo 1080p de 7 min](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z)|
|---|---|---|
| **S1**|Encodage à débit binaire unique. <br/>Fichiers avec une résolution SD ou inférieure, insensibles à l’heure, à moindre coût.|Avec l'option « H264 – Vitesse de transmission unique – 16 x 9 SD », l'encodage à débit unique sous la forme d'un fichier MP4 de résolution SD prend environ 7 minutes.|
| **S2**|Encodage à débit binaire unique et à débit binaire multiple.<br/>Utilisation normale de l’encodage SD et HD.|L'encodage avec la présélection « H264 – Vitesse de transmission unique – 720 pixels » prend environ 6 minutes.<br/><br/>L'encodage avec la présélection « H264 – Vitesse de transmission multiple – 720 pixels » prend environ 12 minutes.|
| **S3**|Encodage à débit binaire unique et à débit binaire multiple.<br/>Vidéos avec une résolution HD complète et 4K. Encodage sensible à l’heure, plus rapide.|L’encodage avec la présélection « H264 à débit binaire simple 1080p » prend environ 3 minutes.<br/><br/>L’encodage avec la présélection « H264 à débit binaire multiple 1080p » prend environ 8 minutes.|

## <a name="considerations"></a>Considérations
> [!IMPORTANT]
> Passez en revue les considérations décrites dans cette section.  
> 
> 

* Pour les travaux d’analyse audio et vidéo déclenchés par Media Services v3 ou Video Indexer, le type d’unité S3 est vivement recommandé.
* Si vous utilisez le pool partagé, c’est-à-dire sans aucune unité réservée, alors vos tâches d’encodage ont les mêmes performances que les unités de requête S1. Toutefois, il n’existe aucune limite supérieure à la durée que vos tâches peuvent passer en file d’attente et, à tout moment, une seule tâche maximum s’exécutera.

## <a name="billing"></a>Facturation

Vous êtes facturé en fonction du nombre de minutes pendant lesquelles les unités réservées Multimédia sont approvisionnées dans votre compte. Ceci est indépendant d'éventuels travaux en cours d'exécution dans votre compte. Pour plus d’informations, consultez la section FAQ de la page [Tarification Media Services](https://azure.microsoft.com/pricing/details/media-services/).   

## <a name="quotas-and-limitations"></a>Quotas et limitations
Pour plus d’informations sur les quotas et les limitations et pour savoir comment ouvrir un ticket de support, consultez la rubrique [Quotas et limitations](media-services-quotas-and-limitations.md).

## <a name="next-step"></a>Étape suivante
Exécuter la tâche de traitement multimédia de mise à l’échelle avec l’une de ces technologies : 

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encoding-units.md)
> * [Portail](media-services-portal-scale-media-processing.md)
> * [REST](/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 

> [!NOTE]
> Pour obtenir la dernière version du kit SDK Java et développer des applications avec Java, consultez [Prise en main du Kit de développement logiciel du client Java pour Azure Media Services](./media-services-java-how-to-use.md). <br/>
> Pour télécharger le dernier kit SDK PHP pour Media Services, recherchez la version 0.5.7 du package Microsoft/WindowAzure dans le [référentiel Packagist](https://packagist.org/packages/microsoft/windowsazure#v0.5.7).  

## <a name="media-services-learning-paths"></a>Parcours d’apprentissage de Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fournir des commentaires
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
