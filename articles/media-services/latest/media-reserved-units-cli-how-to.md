---
title: Utiliser la CLI pour la mise à l’échelle des unités réservées Multimédia - Azure | Microsoft Docs
description: Cette rubrique explique comment utiliser la CLI pour la mise à l’échelle du traitement multimédia avec Azure Media Services.
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
ms.date: 04/24/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 6e7b3b316a8a6dcde95bdf872dbda4cd1372f072
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64721811"
---
# <a name="scaling-media-processing"></a>Mise à l’échelle du traitement multimédia

Azure Media Services vous permet de mettre à l’échelle le traitement multimédia dans votre compte en gérant les unités réservées au multimédia (MRU). Derniers éléments utilisés déterminent la vitesse à laquelle vos tâches de traitement multimédia est traitées. Vous pouvez choisir entre les types d’unités réservées suivantes : **S1**, **S2** ou **S3**. Par exemple, un même travail d’encodage s’exécute plus rapidement quand vous utilisez le type d’unité réservée **S2** que le type **S1**. 

En plus de spécifier le type d’unité réservée, vous pouvez spécifier d’approvisionner votre compte avec des unités réservées. Le nombre d’unités réservées approvisionnées détermine le nombre de tâches multimédias qui peuvent être traitées simultanément dans un compte donné. Si, par exemple, votre compte a cinq unités réservées, les cinq tâches multimédias sont exécutées simultanément tant qu’il y a des tâches à traiter. Les autres tâches restent dans la file d'attente et sont sélectionnées séquentiellement pour le traitement quand l'exécution d'une tâche se termine. Si aucune unité réservée n'est approvisionnée pour un compte donné, les tâches sont sélectionnées séquentiellement. Dans ce cas, le temps d'attente entre l'achèvement d'une tâche et le démarrage de la suivante dépend de la disponibilité des ressources dans le système.

## <a name="choosing-between-different-reserved-unit-types"></a>Choix entre les différents types d’unités réservées

Le tableau suivant vous aide à choisir entre les différentes vitesses d’encodage. Il fournit également quelques cas de test d’évaluation sur [une vidéo que vous pouvez télécharger](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z) pour effectuer vos propres tests :

|Type de RU|Scénario|Exemple de résultats pour la [vidéo 1080p de 7 min](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z)|
|---|---|---|
| **S1**|Encodage à débit binaire unique. <br/>Fichiers avec une résolution SD ou inférieure, insensibles à l’heure, à moindre coût.|Encodage à débit binaire unique SD résolution de fichier MP4 à l’aide de « H264 unique à débit binaire SD 16 x 9 » prend environ sept minutes.|
| **S2**|Encodage à débit binaire unique et à débit binaire multiple.<br/>Utilisation normale de l’encodage SD et HD.|Encodage avec « H264 multidébit 720p » la présélection prend environ 6 minutes.<br/><br/>Encodage avec « H264 multidébit 720p » la présélection prend environ 12 minutes.|
| **S3**|Encodage à débit binaire unique et à débit binaire multiple.<br/>Vidéos avec une résolution HD complète et 4K. Encodage sensible à l’heure, plus rapide.|Encodage avec « H264 multidébit 1 080 p » la présélection prend environ 3 minutes.<br/><br/>Encodage avec « H264 Multiple Bitrate 1080p » la présélection prend environ 8 minutes.|

## <a name="considerations"></a>Considérations

* Pour les travaux d’analyse audio et vidéo déclenchés par Media Services v3 ou Video Indexer, le type d’unité S3 est vivement recommandé.
* Si vous utilisez le pool partagé, c’est-à-dire sans aucune unité réservée, alors vos tâches d’encodage ont les mêmes performances que les unités de requête S1. Toutefois, il n’existe aucune limite supérieure à la durée que vos tâches peuvent passer en file d’attente et, à tout moment, une seule tâche maximum s’exécutera.

Le reste de cet article montre comment utiliser [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref) à l’échelle des derniers éléments utilisés.

> [!NOTE]
> Pour les travaux d’analyse audio et vidéo déclenchés par Media Services v3 ou Video Indexer, nous vous recommandons de provisionner votre compte avec des MRU 10 S3. Si vous avez besoin de plus de 10 MRU S3, ouvrez un ticket de support à l’aide du [Portail Azure](https://portal.azure.com/).
>
> Actuellement, vous ne pouvez pas utiliser le portail Azure pour gérer les autres ressources v3. Utilisez l’[API REST](https://aka.ms/ams-v3-rest-ref), l’interface [CLI](https://aka.ms/ams-v3-cli-ref) ou l’un des kits [SDK](developers-guide.md) pris en charge.

## <a name="prerequisites"></a>Conditions préalables 

[Créer un compte Media Services](create-account-cli-how-to.md).

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="scale-media-reserved-units-with-cli"></a>Mettre à l’échelle les unités réservées au multimédia avec l’interface CLI

Exécutez la commande `mru`.

La commande [az ams account mru](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) suivante définit les unités réservées au multimédia sur le compte « amsaccount » à l’aide des paramètres **count** et **type**.

```azurecli
az ams account mru set -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>Facturation

Vous êtes facturé en nombre de minutes pendant que les unités réservées multimédia sont configurées dans votre compte. Cela se produit indépendamment de si il existe des travaux en cours d’exécution dans votre compte. Pour plus d’informations, consultez la section FAQ de la page [Tarification Media Services](https://azure.microsoft.com/pricing/details/media-services/).   

## <a name="next-step"></a>Étape suivante

[Analyser des vidéos](analyze-videos-tutorial-with-api.md) 

## <a name="see-also"></a>Voir aussi

* [Quotas et limitations](limits-quotas-constraints.md)
* [Interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
