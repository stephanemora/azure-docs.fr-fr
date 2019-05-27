---
title: Création de filtres avec l’API REST Azure Media Services | Microsoft Docs
description: Cette rubrique décrit comment créer des filtres pour que votre client puisse les utiliser pour diffuser des sections spécifiques d'un flux. Media Services crée des manifestes dynamiques pour obtenir cette diffusion sélective.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 05/22/2019
ms.author: juliako
ms.openlocfilehash: 69399513291a47f7109003e825052314f447125a
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/22/2019
ms.locfileid: "66002325"
---
# <a name="creating-filters-with-media-services-rest-api"></a>Créer des filtres avec l’API REST Media Services

Quand vous transmettez votre contenu à un client (événements de streaming en direct ou vidéo à la demande), le fichier manifeste de l’élément multimédia par défaut ne permet pas toujours au client d’interagir avec le contenu comme il le voudrait. Avec Azure Media Services, vous pouvez définir des filtres de compte et d’élément multimédia à appliquer à votre contenu. Pour plus d’informations, consultez [filtres](filters-concept.md) et [manifestes dynamiques](filters-dynamic-manifest-overview.md).

Cette rubrique explique comment définir un filtre pour un élément multimédia Vidéo à la demande et utiliser les API REST pour créer des [Filtres de compte](https://docs.microsoft.com/rest/api/media/accountfilters) et des [Filtres d’élément multimédia](https://docs.microsoft.com/rest/api/media/assetfilters). 

> [!NOTE]
> Veillez à consulter [presentationTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Conditions préalables 

Pour suivre les étapes décrites dans cette rubrique, vous devez :

- Lire [Filtres et manifestes dynamiques](filters-dynamic-manifest-overview.md).
- [Configurer Postman pour les appels d’API REST Azure Media Services](media-rest-apis-with-postman.md).

    Veillez à suivre la dernière étape de la rubrique [Obtenir un jeton Azure AD](media-rest-apis-with-postman.md#get-azure-ad-token). 

## <a name="define-a-filter"></a>Définir un filtre  

L’exemple de **Corps de la demande** suivant définit les conditions de sélection de piste qui sont ajoutées au manifeste. Ce filtre inclut toutes les pistes audio EC-3 et toutes les pistes vidéo dont la vitesse de transmission est comprise entre 0 et 1 000 000.

```json
{
    "properties": {
        "tracks": [
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
                        "operation": "Equal"
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
     }
}
```

## <a name="create-account-filters"></a>Créer des filtres de compte

Dans la collection Postman que vous avez téléchargée, sélectionnez **Filtres de compte**->**Créer ou mettre à jour un filtre de compte**.

La méthode de requête HTTP **PUT** se présente ainsi :

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/accountFilters/{filterName}?api-version=2018-07-01
```

Sélectionnez l’onglet **Corps** et collez le code JSON que vous avez [défini tout à l’heure](#define-a-filter).

Sélectionnez **Envoyer**. 

Le filtre a été créé.

Pour plus d’informations, voir [Créer ou mettre à jour](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate). Voir aussi [Exemples de filtres JSON](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create_an_account_filter).

## <a name="create-asset-filters"></a>Créer des filtres d’élément multimédia  

Dans la collection Postman « Media Services v3 » que vous avez téléchargée, sélectionnez **Éléments**->**Créer ou mettre à jour un filtre d’élément multimédia**.

La méthode de requête HTTP **PUT** se présente ainsi :

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/assets/{assetName}/assetFilters/{filterName}?api-version=2018-07-01
```

Sélectionnez l’onglet **Corps** et collez le code JSON que vous avez [défini tout à l’heure](#define-a-filter).

Sélectionnez **Envoyer**. 

Le filtre d’élément multimédia a été créé.

Pour savoir comment créer ou mettre à jour des filtres d’élément multimédia, voir [Créer ou mettre à jour](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate). Voir aussi [Exemples de filtres JSON](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create_an_asset_filter). 

## <a name="next-steps"></a>Étapes suivantes

[Diffuser des vidéos en streaming](stream-files-tutorial-with-rest.md) 
