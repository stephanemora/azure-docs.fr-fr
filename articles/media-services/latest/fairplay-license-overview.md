---
title: Media Services et prise en charge de la licence Apple FairPlay - Azure | Microsoft Docs
description: Cette rubrique présente une vue d’ensemble de la configuration et des conditions de licence Apple FairPlay.
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2018
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: d348f2696ef865616669af311477cb3a90a59a50
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/09/2020
ms.locfileid: "82995863"
---
# <a name="apple-fairplay-license-requirements-and-configuration"></a>Configuration et conditions de licence Apple FairPlay 

Azure Media Services vous permet de chiffrer votre contenu HLS avec **Apple FairPlay** (AES-128 CBC). Media Services fournit également un service de remise de licences FairPlay. Quand un lecteur tente de lire votre contenu protégé par FairPlay, une demande est envoyée au service de remise de licence pour obtenir une licence. Si le service de licence approuve la requête, il émet la licence qui est envoyée au client et sert à déchiffrer et lire le contenu spécifié.

Media Services propose également des API qui vous permettent de configurer vos licences FairPlay. Cette rubrique décrit les conditions de licence FairPlay et montre comment vous pouvez configurer une licence **FairPlay** à l’aide des API Media Services. 

## <a name="requirements"></a>Spécifications

Les éléments suivants sont nécessaires lors de l’utilisation de Media Services pour chiffrer votre contenu HLS avec **Apple FairPlay** et fournir des licences FairPlay :

* S’inscrire au programme [Apple Developer Program](https://developer.apple.com/).
* Apple exige que le propriétaire du contenu se procure le [package de déploiement](https://developer.apple.com/contact/fps/). Indiquez que vous avez déjà implémenté le module de sécurité des clés (KSM) avec Media Services et que vous avez besoin du package FPS final. Voici les instructions figurant dans le package FPS final pour générer la certification et obtenir la clé secrète d’application (ASK). Utilisez la clé ASK pour configurer FairPlay.
* Les éléments suivants doivent être définis du côté de la remise de clé/licence Media Services :

    * **Certificat d’application** : fichier .pfx qui contient la clé privée. Vous devez créer ce fichier et le chiffrer avec un mot de passe. Le fichier .pfx doit être au format Base64.

        Les étapes suivantes décrivent la génération d’un fichier de certificat .pfx pour FairPlay :

        1. Installez OpenSSL à partir de https://slproweb.com/products/Win32OpenSSL.html.

            Accédez au dossier dans lequel se situent le certificat FairPlay et les autres fichiers fournis par Apple.
        2. Exécutez la commande suivante à partir de la ligne de commande. Elle permet de convertir le fichier .cer en fichier .pem.

            « C:\OpenSSL-Win32\bin\openssl.exe" x509 -inform der -in FairPlay.cer -out FairPlay-out.pem
        3. Exécutez la commande suivante à partir de la ligne de commande. Elle permet de convertir le fichier .pem en fichier .pfx avec la clé privée. Le mot de passe du fichier .pfx est ensuite demandé par OpenSSL.

            « C:\OpenSSL-Win32\bin\openssl.exe" pkcs12 -export -out FairPlay-out.pfx -inkey privatekey.pem -in FairPlay-out.pem -passin file:privatekey-pem-pass.txt
            
    * **Mot de passe du certificat d’application** : mot de passe pour créer le fichier .pfx.
    * **ASK** : cette clé est reçue quand vous générez la certification à l’aide du portail des développeurs Apple. Chaque équipe de développement reçoit une ASK unique. Enregistrez une copie de la clé ASK et stockez-la dans un endroit sûr. Vous devez configurer la clé ASK comme FairPlayAsk avec Media Services.
    
* Les éléments suivants doivent être définis par FPS côté client :

  * **Certificat d’application** : fichier .cer/.der qui contient la clé publique utilisée par le système d’exploitation pour chiffrer une charge utile. Media Services doit le connaître, car il est requis par le lecteur. Le service de remise de clé le déchiffre à l'aide de la clé privée correspondante.

* Pour lire un flux chiffré FairPlay, vous devez obtenir une clé ASK réelle en premier lieu, puis générer un certificat réel. Ce processus crée les trois composants suivants :

  * Fichier .der
  * Fichier .pfx
  * Mot de passe du fichier .pfx

## <a name="fairplay-and-player-apps"></a>Applications FairPlay et de lecteur

Quand votre contenu est chiffré avec **Apple FairPlay**, les échantillons audio et vidéo individuels sont chiffrés à l’aide du mode **AES-128 CBC**. **FairPlay Streaming** (FPS) est intégré dans les systèmes d'exploitation de l’appareil, avec prise en charge native sur iOS et Apple TV. Safari sur OS X active FPS à l’aide de la prise en charge de l’interface EME (Encrypted Media Extensions).

Le lecteur multimédia Azure prend aussi en charge la lecture FairPlay. Pour plus d’informations, consultez [Documentation du lecteur multimédia Azure](https://amp.azure.net/libs/amp/latest/docs/index.html).

Vous pouvez développer vos propres applications de lecteur à l’aide du kit SDK iOS. Pour pouvoir lire le contenu FairPlay, vous devez implémenter le protocole d’échange de licence. Ce protocole n’est pas spécifié par Apple. Chaque application doit décider comment envoyer des requêtes de distribution de clés. Le service de remise de clé Media Services FairPlay s’attend à ce que le certificat SPC soit fourni en tant que message encodé www-form-url au format suivant :

```
spc=<Base64 encoded SPC>
```

## <a name="fairplay-configuration-net-example"></a>Exemple .NET de configuration FairPlay

Vous pouvez utiliser l’API Media Services pour configurer des licences FairPlay. Quand le lecteur tente de lire votre contenu protégé par FairPlay, une demande est envoyée au service de remise de licence pour obtenir la licence. Si le service de licence approuve la demande, le service émet la licence. Elle est envoyée au client et utilisée pour déchiffrer et lire le contenu spécifié.

> [!NOTE]
> Généralement, vous n’aurez à configurer les options de stratégie FairPlay qu’une seule fois, car vous n’aurez qu’un seul jeu de certification et de clé ASK.

L’exemple suivant utilise le kit [SDK .NET Media Services ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models?view=azure-dotnet) pour configurer la licence.

```csharp
private static ContentKeyPolicyFairPlayConfiguration ConfigureFairPlayPolicyOptions()
{

    string askHex = "";
    string FairPlayPfxPassword = "";

    var appCert = new X509Certificate2("FairPlayPfxPath", FairPlayPfxPassword, X509KeyStorageFlags.Exportable);

    byte[] askBytes = Enumerable
        .Range(0, askHex.Length)
        .Where(x => x % 2 == 0)
        .Select(x => Convert.ToByte(askHex.Substring(x, 2), 16))
        .ToArray();

    ContentKeyPolicyFairPlayConfiguration fairPlayConfiguration =
    new ContentKeyPolicyFairPlayConfiguration
    {
        Ask = askBytes,
        FairPlayPfx =
                Convert.ToBase64String(appCert.Export(X509ContentType.Pfx, FairPlayPfxPassword)),
        FairPlayPfxPassword = FairPlayPfxPassword,
        RentalAndLeaseKeyType =
                ContentKeyPolicyFairPlayRentalAndLeaseKeyType
                .PersistentUnlimited,
        RentalDuration = 2249 // in seconds
    };

    return fairPlayConfiguration;
}
```

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [protéger avec DRM](protect-with-drm.md)
