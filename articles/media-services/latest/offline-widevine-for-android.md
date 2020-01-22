---
title: Diffusion en continu Widevine hors connexion pour Android avec Azure Media Services v3
description: Cette rubrique montre comment configurer votre compte Azure Media Services pour la diffusion en continu hors connexion du contenu protégé de Widevine.
services: media-services
keywords: DASH, DRM, Widevine hors connexion, ExoPlayer, Android
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/08/2019
ms.author: willzhan
ms.openlocfilehash: 17416fce887f5f5b952232e284873e539fd68337
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75779907"
---
# <a name="offline-widevine-streaming-for-android"></a>Widevine hors connexion pour Android

Outre la protection de contenu pour la diffusion en continu en ligne, les services d’abonnement et de location du contenu multimédia offrent un contenu téléchargeable qui fonctionne lorsque vous n’êtes pas connecté à internet. Vous devrez peut-être télécharger le contenu sur votre téléphone ou tablette pour une lecture en mode avion, lorsque vous êtes en vol et déconnecté du réseau. Scénarios supplémentaires, dans lesquels vous pouvez souhaiter télécharger du contenu :

- Certains fournisseurs de contenu peuvent interdire la remise de licence de gestion des droits numériques (DRM) au-delà de la frontière d’un pays/d’une région. Si un utilisateur souhaite regarder du contenu en voyage à l’étranger, le téléchargement hors connexion est nécessaire.
- Dans certains pays/certaines régions, la disponibilité Internet ou la bande passante sont limitées. Les utilisateurs peuvent choisir de télécharger du contenu afin de pouvoir le regarder en résolution suffisamment élevée pour une expérience d’affichage satisfaisante.

Cet article explique comment mettre en œuvre la lecture en mode hors connexion pour le contenu DASH protégé par Widevine sur les appareils Android. La gestion des droits numériques (DRM) hors connexion vous permet de fournir des modèles d’abonnement, de location et d’achat pour votre contenu, permettant aux clients de vos services d’emporter facilement du contenu avec eux lorsqu’ils sont déconnectés d’internet.

Pour créer les applications du lecteur Android, nous soulignons trois options :

> [!div class="checklist"]
> * créer un lecteur à l’aide de l’API Java du kit de développement logiciel (SDK) ExoPlayer
> * créer un lecteur à l’aide de la liaison Xamarin du kit de développement logiciel (SDK) ExoPlayer
> * créer un lecteur à l’aide de Encrypted Media Extension (EME) et Media Source Extension (MSE) dans le navigateur mobile Chrome v62 ou version ultérieure

L’article répond également à certaines questions fréquentes sur la diffusion en continu du contenu protégé de Widevine.

> [!NOTE]
> DRM hors connexion est uniquement facturé pour une requête unique de licence lorsque vous téléchargez le contenu. Les erreurs ne sont pas facturées.

## <a name="prerequisites"></a>Conditions préalables requises 

Avant de mettre en œuvre la DRM hors connexion pour Widevine sur des appareils Android, vous devez, tout d’abord :

- Vous familiariser avec les concepts présentés pour la protection du contenu en ligne à l’aide de la DRM de Widevine. Vous trouverez toutes les informations nécessaires dans les documents/exemples suivants :
    - [Concevoir un système de protection de contenu multi-DRM avec contrôle d’accès](design-multi-drm-system-with-access-control.md)
    - [Utilisation du chiffrement dynamique DRM et du service de remise des licences](protect-with-drm.md)
- Clonez https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git.

    Pour ajouter des configurations Widevine, vous devrez modifier le code fourni dans [Encrypt with DRM using .NET](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM).  
- Vous familiariser avec le kit de développement logiciel (SDK) Google ExoPlayer pour Android, un kit de développement logiciel (SDK) de lecteur vidéo open source capable de prendre en charge la lecture de la DRM de Widevine hors connexion. 
    - [Kit de développement logiciel SDK ExoPlayer](https://github.com/google/ExoPlayer)
    - [Guide du Développeur Exoplayer](https://google.github.io/ExoPlayer/guide.html)
    - [Blog du Développeur Exoplayer](https://medium.com/google-exoplayer)

## <a name="configure-content-protection-in-azure-media-services"></a>Configurer la protection du contenu dans Azure Media Services

Dans la méthode [GetOrCreateContentKeyPolicyAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L189), les étapes nécessaires suivantes sont présentes :

1. Spécifiez comment la distribution des clés de contenu doit être autorisée dans le service de distribution des licences : 

    ```csharp
    ContentKeyPolicySymmetricTokenKey primaryKey = new ContentKeyPolicySymmetricTokenKey(tokenSigningKey);
    List<ContentKeyPolicyTokenClaim> requiredClaims = new List<ContentKeyPolicyTokenClaim>()
    {
        ContentKeyPolicyTokenClaim.ContentKeyIdentifierClaim
    };
    List<ContentKeyPolicyRestrictionTokenKey> alternateKeys = null;
    ContentKeyPolicyTokenRestriction restriction 
        = new ContentKeyPolicyTokenRestriction(Issuer, Audience, primaryKey, ContentKeyPolicyRestrictionTokenType.Jwt, alternateKeys, requiredClaims);
    ```
2. Configurez le modèle de licence Widevine :  

    ```csharp
    ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
    ```

3. Créez ContentKeyPolicyOptions :

    ```csharp
    options.Add(
        new ContentKeyPolicyOption()
        {
            Configuration = widevineConfig,
            Restriction = restriction
        });
    ```

## <a name="enable-offline-mode"></a>Activer le mode hors connexion

Pour activer le mode **hors connexion** pour les licences Widevine, vous devez configurer le [modèle de licence Widevine](widevine-license-template-overview.md). Dans l’objet **policy_overrides**, définissez la propriété **can_persist** sur **true** (la valeur par défaut est false), comme indiqué dans [ConfigureWidevineLicenseTempate](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L563). 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#ConfigureWidevineLicenseTempate)]

## <a name="configuring-the-android-player-for-offline-playback"></a>Configuration du lecteur Android pour la lecture hors connexion

Le moyen le plus simple de développer une application de lecteur natif pour les appareils Android est d’utiliser le [Kit de développement logiciel (SDK) Google ExoPlayer](https://github.com/google/ExoPlayer), un kit de développement logiciel du lecteur vidéo open source. ExoPlayer prend en charge les fonctionnalités non actuellement prises en charge par les API MediaPlayer native d’Android, y compris les protocoles de remise MPEG-DASH et Microsoft Smooth Streaming.

ExoPlayer version 2.6 et version supérieure inclut de nombreuses classes qui prennent en charge la lecture de la DRM de Widevine hors connexion. En particulier, la classe OfflineLicenseHelper fournit des fonctions utilitaires pour faciliter l’utilisation de la DefaultDrmSessionManager pour le téléchargement, le renouvellement et la libération des licences hors connexion. Les classes fournies dans le dossier du kit de développement logiciel "library/core/src/main/java/com/google/android/exoplayer2/offline/" prennent en charge le téléchargement du contenu vidéo hors connexion.

Les classes suivantes permettent le mode hors connexion dans le SDK ExoPlayer pour Android :

- library/core/src/main/java/com/google/android/exoplayer2/drm/OfflineLicenseHelper.java  
- library/core/src/main/java/com/google/android/exoplayer2/drm/DefaultDrmSession.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/DefaultDrmSessionManager.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/DrmSession.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/ErrorStateDrmSession.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/ExoMediaDrm.java
- library/core/src/main/java/com/google/android/exoplayer2/offline/SegmentDownloader.java
- library/core/src/main/java/com/google/android/exoplayer2/offline/DownloaderConstructorHelper.java 
- library/core/src/main/java/com/google/android/exoplayer2/offline/Downloader.java
- library/dash/src/main/java/com/google/android/exoplayer2/source/dash/offline/DashDownloader.java 

Les développeurs doivent référencer le [Guide du développeur ExoPlayer](https://google.github.io/ExoPlayer/guide.html) et [Blog des développeurs](https://medium.com/google-exoplayer) correspondant pendant le développement d’une application. Google n’a pas encore publié d’implémentation de référence entièrement documentée ni d’exemple de code pour l’application ExoPlayer qui prend en charge Widevine hors connexion. Les informations sont donc limitées au guide et au blog des développeurs. 

### <a name="working-with-older-android-devices"></a>Utilisation des anciens appareils Android

En ce qui concerne certains anciens appareils Android, vous devez définir des valeurs pour les propriétés **policy_overrides** suivantes (définies dans le [modèle de licence Widevine ](widevine-license-template-overview.md) : **rental_duration_seconds**, **playback_duration_seconds** et **license_duration_seconds**. Ou bien, vous pouvez les définir à zéro, ce qui signifie une durée illimitée/infinie.  

Les valeurs doivent être définies pour éviter un bogue de dépassement de capacité d’entier. Pour en savoir plus sur le problème, consultez https://github.com/google/ExoPlayer/issues/3150 et https://github.com/google/ExoPlayer/issues/3112. <br/>Si vous ne définissez pas explicitement les valeurs, des valeurs très élevées pour **PlaybackDurationRemaining** et **LicenseDurationRemaining** seront affectées, (par exemple, 9223372036854775807, qui est la valeur positive maximale pour un entier de 64 bits). Par conséquent, la licence Widevine s’affiche comme expirée, le déchiffrement n’a donc pas lieu. 

Ce problème ne se produit pas sur Android Lollipop 5.0 ou version ultérieure, Android 5.0 étant la première version Android conçue pour prendre entièrement en charge des plateformes ARMv8 ([Advanced RISC Machine](https://en.wikipedia.org/wiki/ARM_architecture)) et 64 bits, tandis qu’Android 4.4 KitKat a été conçu à l’origine pour prendre en charge des plateformes ARMv7 et 32 bits comme pour d’autres versions Android plus anciennes.

## <a name="using-xamarin-to-build-an-android-playback-app"></a>Utilisation de Xamarin pour générer une application de lecture Android

Vous pouvez rechercher des liaisons Xamarin pour ExoPlayer à l’aide des liens suivants :

- [Bibliothèque de liaisons Xamarin pour la bibliothèque de Google ExoPlayer](https://github.com/martijn00/ExoPlayerXamarin)
- [Liaisons Xamarin pour ExoPlayer NuGet](https://www.nuget.org/packages/Xam.Plugins.Android.ExoPlayer/)

Consultez également le thread suivant : [Liaison Xamarin](https://github.com/martijn00/ExoPlayerXamarin/pull/57). 

## <a name="chrome-player-apps-for-android"></a>Applications du lecteur Chrome pour Android

À partir de la version de [Chrome pour Android v. 62](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates), la licence persistant dans EME est prise en charge. [Widevine L1](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates#widevine_l1) est maintenant également pris en charge dans Chrome pour Android. Cela vous permet de créer des applications de lecture hors connexion dans Chrome si vos utilisateurs finaux disposent de cette version de Chrome (ou version ultérieure). 

En outre, Google a généré un exemple de Progressive Web App (PWA), procuré en open source : 

- [Code source](https://github.com/GoogleChromeLabs/sample-media-pwa)
- [Version Google hébergée](https://biograf-155113.appspot.com/ttt/episode-2/) (fonctionne uniquement dans Chrome v 62 et version ultérieure sur les appareils Android)

Si vous mettez à niveau votre navigateur Chrome mobile sur la v62 (ou version ultérieure) sur un téléphone Android et testez l’exemple d’application hébergée ci-dessus, vous constaterez que la lecture en continu en ligne et la lecture hors connexion fonctionnent.

L’application PWA open source ci-dessus a été créée dans Node.js. Si vous souhaitez héberger votre propre version sur un serveur Ubuntu, tenez compte des problèmes fréquemment rencontrés suivants qui peuvent empêcher la lecture :

1. Problème CORS : l’échantillon vidéo de l’exemple d’application est hébergé dans https://storage.googleapis.com/biograf-video-files/videos/. Google a défini CORS pour tous les exemples de test hébergés dans le compartiment de stockage Cloud de Google. Ils sont pris en charge avec les en-têtes CORS, en spécifiant explicitement l’entrée CORS : https://biograf-155113.appspot.com (domaine dans lequel Google héberge ses exemples) empêchant l’accès par d’autres sites. Si vous essayez, vous verrez l’erreur HTTP suivante : Impossible de charger https://storage.googleapis.com/biograf-video-files/videos/poly-sizzle-2015/mp4/dash.mpd: Aucun en-tête « Access-Control-Allow-Origin » n’est présent sur la ressource demandée. L’accès à l’adresse ’https:\//13.85.80.81:8080’ d’origine n’est donc pas autorisé. Si une réponse opaque répond à vos besoins, définissez le mode de la requête sur « no-cors » pour extraire la ressource avec CORS désactivé.
2. Problème de certificat : À partir de Chrome v58, EME pour Widevine nécessite HTTPS. Par conséquent, vous devez héberger l’exemple d’application via le protocole HTTPS avec un certificat X509. Les certificats de test habituels ne fonctionnent pas en raison des exigences suivantes : Vous devez obtenir un certificat répondant aux exigences minimales suivantes :
    - Chrome et Firefox exige que le paramètre Nom alternatif de l’objet SAN existe dans le certificat
    - Le certificat doit avoir une certification approuvée et un certificat auto-signé de développement ne fonctionne pas
    - Le certificat doit avoir un CN correspondant au nom DNS de la passerelle ou du serveur web

## <a name="frequently-asked-questions"></a>Forum aux questions

### <a name="question"></a>Question

Comment puis-je remettre des licences persistantes (activées en mode hors connexion) pour certains clients/utilisateurs et des licences non persistantes (désactivées en mode hors connexion) pour d’autres ? Dois-je dupliquer le contenu et utiliser une clé de contenu distincte ?

### <a name="answer"></a>Réponse
Étant donné que Media Services v3 permet à un actif multimédia d’avoir plusieurs StreamingLocators, vous pouvez avoir :

1.  Un ContentKeyPolicy avec license_type = "persistent", ContentKeyPolicyRestriction avec claim sur "persistent" ainsi que son StreamingLocator
2.  Un autre ContentKeyPolicy avec license_type = "nonpersistent", ContentKeyPolicyRestriction avec claim sur "nonpersistent" ainsi que son StreamingLocator
3.  Les deux StreamingLocators ont un ContentKey différent.

En fonction de la logique métier du service d’émission de jeton de sécurité personnalisé, différentes revendications peuvent être émises dans le jeton JWT. Avec le jeton, seule la licence correspondante peut être obtenue et seule l’URL correspondante peut être lue.

### <a name="question"></a>Question

Pour les niveaux de sécurité Widevine dans la documentation [doc Vue d’ensemble de l’architecture DRM Widevine](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf) de Google, il définit trois différents niveaux de sécurité. Toutefois, dans [Documentation Azure Media Services sur le modèle de licence Widevine](widevine-license-template-overview.md), cinq niveaux de sécurité différents sont soulignés. Quelle est la relation ou le mappage entre les deux différents ensembles de niveaux de sécurité ?

### <a name="answer"></a>Réponse

Dans la [Vue d’ensemble de l’architecture DRM Widevine](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf) de Google, il définit les trois niveaux de sécurité suivants :

1.  Niveau de sécurité 1 : tous les traitements, chiffrements et contrôles du contenu sont effectués au sein de l’environnement TEE (Trusted Execution Environment). En ce qui concerne certains modèles de mise en œuvre, le traitement de la sécurité peut être effectué dans différentes puces.
2.  Niveau de sécurité 2 : effectue le chiffrement (mais pas le traitement vidéo) dans l’environnement TEE : les mémoires tampons déchiffrées sont retournées au domaine d’application et traitées par le biais d’un matériel ou d’un logiciel vidéo distinct. Au niveau 2, toutefois, les informations de chiffrement sont toujours traitées uniquement dans l’environnement TEE.
3.  Le niveau de sécurité 3 n’a pas d’environnement TEE sur l’appareil. Les mesures appropriées peuvent être prises pour protéger les informations de chiffrement et le contenu déchiffré sur le système d’exploitation hôte. Une mise en œuvre de niveau 3 peut également inclure un moteur de chiffrement matériel, mais qui améliore seulement la performance, pas la sécurité.

Dans le même temps, dans [Documentation Azure Media Services sur le modèle de licence Widevine](widevine-license-template-overview.md), la propriété security_level de content_key_specs peut avoir les cinq différentes valeurs suivantes (exigences de robustesse client pour la lecture) :

1.  Le chiffrement whitebox basé sur le logiciel est requis.
2.  Le chiffrement logiciel et un décodeur masqué sont requis.
3.  Les opérations de matériel clé et de chiffrement doivent être effectuées dans un environnement TEE soutenu par le matériel.
4.  Le chiffrement et le décodage du contenu doivent être effectués dans un environnement TEE soutenu par le matériel.
5.  Le chiffrement, le décodage et le traitement du support (compressé et décompressé) doivent être gérés dans un environnement TEE soutenu par le matériel.

Les deux niveaux de sécurité sont définis par Google Widevine. La différence réside dans son niveau d’utilisation : niveau d’architecture ou d’API. Les cinq niveaux de sécurité sont utilisés dans l’API Widevine. L’objet content_key_specs, qui contient security_level est désérialisé et transmis au service de remise globale Widevine par le service de licence Azure Media Services Widevine. Le tableau ci-dessous montre le mappage entre les deux ensembles de niveaux de sécurité.

| **Niveaux de sécurité définis dans l’architecture de Widevine** |**Niveaux de sécurité utilisés dans l’API de Widevine**|
|---|---| 
| **Niveau de sécurité 1** : tous les traitements, chiffrements et contrôles du contenu sont effectués au sein de l’environnement TEE (Trusted Execution Environment). En ce qui concerne certains modèles de mise en œuvre, le traitement de la sécurité peut être effectué dans différentes puces.|**security_level=5** : Le chiffrement, le décodage et le traitement du support (compressé et décompressé) doivent être gérés dans un environnement TEE soutenu par le matériel.<br/><br/>**security_level=4** : Le chiffrement et le décodage du contenu doivent être effectués dans un environnement TEE soutenu par le matériel.|
**Niveau de sécurité 2** : effectue le chiffrement (mais pas le traitement vidéo) dans l’environnement TEE : les mémoires tampons déchiffrées sont retournées au domaine d’application et traitées par le biais d’un matériel ou d’un logiciel vidéo distinct. Au niveau 2, toutefois, les informations de chiffrement sont toujours traitées uniquement dans l’environnement TEE.| **security_level=3** : Les opérations de matériel clé et de chiffrement doivent être effectuées dans un environnement TEE soutenu par le matériel. |
| **Niveau de sécurité 3** : l’environnement TEE ne se trouve pas sur l’appareil. Les mesures appropriées peuvent être prises pour protéger les informations de chiffrement et le contenu déchiffré sur le système d’exploitation hôte. Une mise en œuvre de niveau 3 peut également inclure un moteur de chiffrement matériel, mais qui améliore seulement la performance, pas la sécurité. | **security_level=2** : le chiffrement logiciel et un décodeur masqué sont nécessaires.<br/><br/>**security_level=1** : Le chiffrement whitebox basé sur le logiciel est requis.|

### <a name="question"></a>Question

Pourquoi le téléchargement de contenu prend-il tant de temps ?

### <a name="answer"></a>Réponse

Il existe deux façons d’améliorer la vitesse du téléchargement :

1.  Activer le CDN afin que les utilisateurs finaux soient plus susceptibles d’atteindre le CDN au lieu du point de terminaison de streaming pour le téléchargement du contenu. Si l’utilisateur atteint un point de terminaison de streaming, chaque segment HLS ou fragment DASH est dynamiquement empaqueté et chiffré. Bien que cette latence soit dans une échelle de millisecondes pour chaque segment/fragment, lorsque vous disposez d’une vidéo d’une durée d’une heure, la latence cumulée peut être volumineuse, entraînant un téléchargement plus long.
2.  Fournir aux utilisateurs finaux la possibilité de télécharger de manière sélective des couches de qualité vidéo et des pistes audio plutôt que tout le contenu. En ce qui concerne le mode hors connexion, il est inutile de télécharger toutes les couches de qualité. Il existe deux moyens de parvenir à cet objectif :
    1.  Contrôlé par le client : sélection automatique d’applications de lecteur ou sélection utilisateur de la couche de qualité vidéo et des pistes audio à télécharger ;
    2.  Contrôlé par le service : il est possible d’utiliser la fonctionnalité manifeste dynamique dans Azure Media Services pour créer un filtre (global), ce qui limite la sélection de HLS ou DASH MPD à une couche unique de qualité vidéo et des pistes audio sélectionnées. Ainsi, l’URL de téléchargement présentée aux utilisateurs finaux inclut ce filtre.

## <a name="additional-notes"></a>Remarques supplémentaires

* Widevine est un service fourni par Google Inc. soumis aux conditions de service et à la politique de confidentialité de Google, Inc.

## <a name="summary"></a>Résumé

Cet article explique comment mettre en œuvre la lecture en mode hors connexion pour le contenu DASH protégé par Widevine sur les appareils Android.  L’article répond également à certaines questions fréquentes sur la diffusion en continu du contenu protégé de Widevine.
