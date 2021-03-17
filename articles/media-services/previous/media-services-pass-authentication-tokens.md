---
title: Transmettre des jetons d’authentification à Azure Media Services | Microsoft Docs
description: Découvrez comment envoyer des jetons d’authentification entre le client et le service de remise de clés d’Azure Media Services
services: media-services
keywords: protection de contenu, DRM, authentification de jeton
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: ba4daec8ef5f2cc1da3a211113e7236dfd22803d
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103008237"
---
# <a name="learn-how-clients-pass-tokens-to-the-azure-media-services-key-delivery-service"></a>Découvrez comment les clients passent des jetons au service de remise de clés d’Azure Media Services

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Les clients s’interrogent souvent sur la manière dont lecteur peut transmettre des jetons au service de remise de clés d’Azure Media Services à des fins de vérification afin que le lecteur puisse obtenir la clé. Media Services prend en charge les formats SWT (Simple Web Tokens) et JWT (JSON Web Token). L’authentification par jeton est appliquée à tout type de clé, que vous utilisiez un chiffrement commun ou un chiffrement d’enveloppe AES (Advanced Encryption Standard) dans le système.

 Selon le lecteur et la plateforme ciblés, vous pouvez transmettre le jeton à votre lecteur de différentes manières :

- Via l’en-tête d’autorisation HTTP.
    > [!NOTE]
    > Le préfixe « Porteur » est attendu pour les spécifications OAuth 2.0. Un exemple de lecteur avec la configuration de jeton est hébergé sur la [page de démo](https://ampdemo.azureedge.net/) Azure Media Player. Pour définir la source vidéo, sélectionnez **AES (jeton JWT)** ou **AES (jeton SWT)** . Le jeton est passé via l’en-tête d’autorisation.

- En ajoutant un paramètre de requête URL avec « token=tokenvalue ».  
    > [!NOTE]
    > Le préfixe « Porteur » n’est pas attendu. Puisque le jeton est envoyé via une URL, vous devrez blinder la chaîne de jetons. Voici un exemple de code C# qui illustre la manière de procéder :

    ```csharp
    string armoredAuthToken = System.Web.HttpUtility.UrlEncode(authToken);
    string uriWithTokenParameter = string.Format("{0}&token={1}", keyDeliveryServiceUri.AbsoluteUri, armoredAuthToken);
    Uri keyDeliveryUrlWithTokenParameter = new Uri(uriWithTokenParameter);
    ```

- Via le champ CustomData.
Cette option est utilisée uniquement pour l’acquisition de licence PlayReady, via le champ CustomData du défi d’acquisition de la licence PlayReady. Dans ce cas, le jeton doit se trouver à l’intérieur du document XML décrit ici :

    ```xml
    <?xml version="1.0"?>
    <CustomData xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyCustomData/v1"> 
        <Token></Token> 
    </CustomData>
    ```
    Placez votre jeton d’authentification dans l’élément Token.

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
