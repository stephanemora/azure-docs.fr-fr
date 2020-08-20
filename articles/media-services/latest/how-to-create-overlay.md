---
title: Comment créer une superposition à l’aide de Media Encoder Standard
description: Découvrez comment créer une superposition à l’aide de Media Encoder Standard.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 07/27/2020
ms.openlocfilehash: 85b1b6c56221deaa03057a7ccb658b4bf67124eb
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87830578"
---
# <a name="how-to-create-an-overlay-with-media-encoder-standard"></a>Comment créer une superposition à l’aide de Media Encoder Standard

Media Encoder Standard vous permet de superposer une image sur une vidéo existante. Les formats suivants sont actuellement pris en charge : png, jpg, gif et bmp.

## <a name="prerequisites"></a>Conditions préalables requises

* Collectez les informations de compte dont vous avez besoin pour configurer le fichier *appsettings.json* dans l’exemple. Si vous n’êtes pas sûr de savoir comment procéder, consultez [Démarrage rapide : Inscrire une application avec la plateforme des identités Microsoft](../../active-directory/develop/quickstart-register-app.md). Les valeurs suivantes sont attendues dans le fichier *appsettings.json*.

    ```json
    {
    "AadClientId": "",
    "AadEndpoint": "https://login.microsoftonline.com",
    "AadSecret": "",
    "AadTenantId": "",
    "AccountName": "",
    "ArmAadAudience": "https://management.core.windows.net/",
    "ArmEndpoint": "https://management.azure.com/",
    "Region": "",
    "ResourceGroup": "",
    "SubscriptionId": ""
    }
    ```

Si vous n’êtes pas déjà familiarisé avec les transformations, il est recommandé d’effectuer les activités suivantes :

* Lisez [Encodage vidéo et audio avec Media Services](encoding-concept.md).
* Lisez [Comment encoder avec une transformation personnalisée – .NET](customize-encoder-presets-how-to.md). Suivez les étapes de cet article pour configurer le .NET nécessaire à l’utilisation des transformations, puis revenez ici pour tester un exemple de présélection de superpositions.
* Consultez le [document de référence sur les transformations](/rest/api/media/transforms).

Une fois que vous êtes familiarisé avec les transformations, téléchargez l’exemple de superpositions.

## <a name="overlays-preset-sample"></a>Exemple de présélection de superpositions

Téléchargez l’[exemple media-services-overlay](https://github.com/Azure-Samples/media-services-overlays) pour prendre en main les superpositions.

## <a name="next-steps"></a>Étapes suivantes

* [Sous-découper une vidéo lors de l’encodage avec Media Services - .NET](subclip-video-dotnet-howto.md)