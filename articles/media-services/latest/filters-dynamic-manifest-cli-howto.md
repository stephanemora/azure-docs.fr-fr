---
title: Utiliser l’interface CLI pour créer des filtres avec Azure Media Services | Microsoft Docs
description: Cette rubrique montre comment utiliser l’interface CLI pour créer des filtres avec Media Services.
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
ms.date: 06/03/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: fd581d1dfea850e925909df59f2d4fdd421053fb
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66494366"
---
# <a name="creating-filters-with-cli"></a>Créer des filtres avec l’interface CLI 

Quand vous transmettez votre contenu à un client (événements de streaming en direct ou vidéo à la demande), le fichier manifeste de l’actif multimédia par défaut ne permet peut-être pas au client d’interagir avec le contenu comme il le voudrait. Avec Azure Media Services, vous pouvez définir des filtres de compte et d’élément multimédia à appliquer à votre contenu. 

Pour obtenir une description détaillée de cette fonctionnalité et les scénarios où il est utilisé, consultez [manifestes dynamiques](filters-dynamic-manifest-overview.md) et [filtres](filters-concept.md).

Cette rubrique explique comment définir un filtre pour un actif multimédia Vidéo à la demande et comment utiliser l’interface CLI dans Media Services v3 pour créer des [filtres de compte](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) et des [filtres d’actif multimédia](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest). 

> [!NOTE]
> Veillez à consulter [presentationTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Conditions préalables 

- [Créer un compte Media Services](create-account-cli-how-to.md). Veillez à mémoriser le nom du groupe de ressources et le nom du compte Media Services. 

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="define-a-filter"></a>Définir un filtre 

L’exemple suivant définit les conditions de sélection de piste qui sont ajoutées au manifeste final. Ce filtre inclut toutes les pistes audio EC-3 et toutes les pistes vidéo dont la vitesse de transmission est comprise entre 0 et 1 000 000.

> [!TIP]
> Si vous envisagez de définir **filtres** dans REST, notez que vous devez inclure l’objet JSON de wrapper « Propriétés ».  

```json
[
    {
        "trackSelections": [
            {
                "property": "Type",
                "value": "Audio",
                "operation": "Equal"
            },
            {
                "property": "FourCC",
                "value": "EC-3",
                "operation": "NotEqual"
            }
        ]
    },
    {
        "trackSelections": [
            {
                "property": "Type",
                "value": "Video",
                "operation": "Equal"
            },
            {
                "property": "Bitrate",
                "value": "0-1000000",
                "operation": "Equal"
            }
        ]
    }
]
```

## <a name="create-account-filters"></a>Créer des filtres de compte

La commande [az ams account-filter](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) ci-dessous crée un filtre de compte qui filtre les sélections de pistes [définies précédemment](#define-a-filter). 

La commande vous permet de transmettre paramètre `--tracks` facultatif contenant un JSON qui représente les sélections de pistes.  Utilisez @{file} pour charger le JSON à partir d’un fichier. Si vous utilisez l’interface de ligne de commande Azure en local, spécifiez le chemin d’accès de la totalité du fichier :

```azurecli
az ams account-filter create -a amsAccount -g resourceGroup -n filterName --tracks @tracks.json
```

Voir aussi [Exemples de filtres JSON](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create_an_account_filter).

## <a name="create-asset-filters"></a>Créer des filtres d’actif multimédia

La commande [az ams asset-filter](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest) ci-dessous crée un filtre d’actif multimédia qui filtre les sélections de pistes [définies précédemment](#define-a-filter). 

```azurecli
az ams asset-filter create -a amsAccount -g resourceGroup -n filterName --asset-name assetName --tracks @tracks.json
```

Voir aussi [Exemples de filtres JSON](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create_an_asset_filter).


## <a name="associate-filters-with-streaming-locator"></a>Associer des filtres de localisateur de diffusion en continu

Vous pouvez spécifier une liste de filtres de compte d’actif ou, s’applique également à votre localisateur de diffusion en continu. Le [l’empaquetage dynamique (point de terminaison de diffusion en continu)](dynamic-packaging-overview.md) s’applique à cette liste de filtres avec ceux de votre client spécifie l’URL. Cette combinaison génère une [manifeste dynamique](filters-dynamic-manifest-overview.md), qui est basé sur les filtres dans l’URL + filtres que vous spécifiez dans le localisateur de diffusion en continu. Nous vous recommandons d’utiliser cette fonctionnalité si vous souhaitez appliquer des filtres, mais ne souhaitez pas exposer les noms de filtre dans l’URL.

Le code CLI suivant montre comment créer un localisateur de diffusion en continu et spécifiez `filters`. Il s’agit d’une propriété facultative qui accepte une liste séparée par des espaces de noms des éléments de filtre et/ou les noms de compte de filtre.

```azurecli
az ams streaming-locator create -a amsAccount -g resourceGroup -n streamingLocatorName \
                                --asset-name assetName \                               
                                --streaming-policy-name policyName \
                                --filters filterName1 filterName2
                                
```

## <a name="stream-using-filters"></a>Stream à l’aide de filtres

Lorsque vous définissez des filtres, vos clients peuvent les utiliser dans l'URL de diffusion en continu. Il serait possible d’appliquer des filtres à des protocoles de streaming à débit adaptatif : HLS (HTTP Live Streaming) Apple, MPEG-DASH et Smooth Streaming.

Le tableau suivant présente des exemples d’URL utilisant des filtres :

|Protocol|Exemples|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-step"></a>Étape suivante

[Diffuser des vidéos en streaming](stream-files-tutorial-with-api.md) 

## <a name="see-also"></a>Voir aussi

[Interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
