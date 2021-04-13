---
title: Passer des jetons d’authentification à Media Services v3 | Microsoft Docs
description: Découvrez comment envoyer des jetons d’authentification entre le client et le service de remise de clés Media Services v3
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 590309ad392876ba3c5cb6d21abe777ab5a93efb
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106281370"
---
# <a name="pass-tokens-to-the-azure-media-services-v3-key-delivery-service"></a>Passer des jetons au service de remise de clés Azure Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Les clients s’interrogent souvent sur la manière dont lecteur peut transmettre des jetons au service de remise de clés d’Azure Media Services à des fins de vérification afin que le lecteur puisse obtenir la clé. Media Services prend en charge les formats SWT (Simple Web Tokens) et JWT (JSON Web Token). L’authentification par jeton est appliquée à tout type de clé, que vous utilisiez un chiffrement commun ou un chiffrement d’enveloppe AES (Advanced Encryption Standard) dans le système.

 Selon le lecteur et la plateforme ciblés, vous pouvez transmettre le jeton à votre lecteur de différentes manières :

## <a name="pass-a-token-through-the-http-authorization-header"></a>Passer un jeton via l’en-tête d’autorisation HTTP

> [!NOTE]
> Le préfixe « Porteur » est attendu pour les spécifications OAuth 2.0. Pour définir la source vidéo, sélectionnez **AES (jeton JWT)** ou **AES (jeton SWT)** . Le jeton est passé via l’en-tête d’autorisation.

## <a name="pass-a-token-via-the-addition-of-a-url-query-parameter-with-tokentokenvalue"></a>Passez un jeton via l’ajout d’un paramètre de requête URL avec « token = tokenvalue ».

> [!NOTE]
> Le préfixe « Porteur » n’est pas attendu. Puisque le jeton est envoyé via une URL, vous devrez blinder la chaîne de jetons. Voici un exemple de code C# qui illustre la manière de procéder :

```csharp
    string armoredAuthToken = System.Web.HttpUtility.UrlEncode(authToken);
    string uriWithTokenParameter = string.Format("{0}&token={1}", keyDeliveryServiceUri.AbsoluteUri, armoredAuthToken);
    Uri keyDeliveryUrlWithTokenParameter = new Uri(uriWithTokenParameter);
```

## <a name="pass-a-token-through-the-customdata-field"></a>Passer un jeton via le champ CustomData

Cette option est utilisée uniquement pour l’acquisition de licence PlayReady, via le champ CustomData du défi d’acquisition de la licence PlayReady. Dans ce cas, le jeton doit se trouver à l’intérieur du document XML décrit ici :

```xml
    <?xml version="1.0"?>
    <CustomData xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyCustomData/v1"> 
        <Token></Token> 
    </CustomData>
```

Placez votre jeton d’authentification dans l’élément Token.
