---
title: Protéger votre contenu avec Azure Media Services | Microsoft Docs
description: Cet article donne une vue d’ensemble de la protection du contenu avec Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: juliako
ms.openlocfilehash: f8ef92a335dd6faee076356dbffc873b08afbdc0
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49394258"
---
# <a name="content-protection-overview"></a>Présentation de la protection du contenu

Vous pouvez utiliser Azure Media Services pour sécuriser votre contenu multimédia du moment où il quitte votre ordinateur jusqu’à la remise, en passant par le stockage et le traitement. Media Services vous permet de transmettre votre contenu dynamique ou à la demande chiffré dynamiquement avec la norme Advanced Encryption Standard (AES-128) ou un des principaux systèmes de gestion des droits numériques (DRM) : Microsoft PlayReady, Google Widevine et Apple FairPlay. Media Services fournit également un service de distribution de clés AES et licences (PlayReady, Widevine et FairPlay) DRM aux clients autorisés. 

L’image suivante illustre le flux de travail de protection du contenu Media Services : 

![Protéger du contenu](./media/content-protection/content-protection.png)

&#42; *le chiffrement dynamique prend en charge les standards AES-128 « clé en clair », CBCS et CENC. Pour plus d’informations, consultez la matrice de prise en charge [ici](#streaming-protocols-and-encryption-types).*

Cet article explique les concepts et la terminologie pertinents pour comprendre la protection du contenu avec Media Services. Il comporte également une section [FAQ](#faq) et fournit des liens d’accès aux articles indiquant comment protéger le contenu. 

## <a name="main-components-of-the-content-protection-system"></a>Principaux composants du système de protection du contenu

Pour mener à bien votre conception de système/d’application de « protection de contenu », vous devez comprendre pleinement l’étendue de l’effort. La liste suivante donne un aperçu des trois éléments que vous devez implémenter. 

1. Code Azure Media Services
  
  * Modèles de licence pour PlayReady, Widevine et/ou FairPlay. Les modèles vous permettent de configurer les droits et autorisations pour chacun des DRM utilisés
  * Autorisation de remise de licence, spécifiant la logique de vérification d’autorisation basée sur les revendications dans JWT
  * Clés de contenu, protocoles de diffusion en continu et DRM correspondants appliqués, définissant le chiffrement DRM

  > [!NOTE]
  > Vous pouvez chiffrer chaque élément multimédia avec plusieurs types de chiffrement (AES-128, PlayReady, Widevine, FairPlay). Consultez les [protocoles de diffusion en continu et les types de chiffrement](#streaming-protocols-and-encryption-types) pour identifier la meilleure combinaison.
  
  Les articles suivants décrivent les étapes pour chiffrer du contenu avec AES et/ou DRM : 
  
  * [Protection avec le chiffrement AES](protect-with-aes128.md)
  * [Protéger avec DRM](protect-with-drm.md)

2. Lecteur avec client AES ou DRM. Une application de lecteur vidéo basée sur un kit de développement logiciel du lecteur (natif ou dans le navigateur) doit remplir les conditions suivantes :
  * Le Kit de développement logiciel du lecteur prend en charge les clients DRM nécessaires
  * Le Kit de développement logiciel du lecteur prend en charge les protocoles de diffusion en continu requis : Smooth, DASH et/ou HLS
  * Le Kit de développement logiciel du lecteur doit être en mesure de gérer le passage d’un jeton JWT lors de la requête d’acquisition de licence
  
    Vous pouvez créer un lecteur à l’aide de [l’API Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/). Utilisez [l’API ProtectionInfo d’Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/) pour spécifier la technologie DRM à utiliser sur d’autres plateformes DRM.

    Pour tester du contenu chiffré par AES ou CENC (Widevine et/ou PlayReady), vous pouvez utiliser le [lecteur multimédia Azure](https://ampdemo.azureedge.net/azuremediaplayer.html). Veillez à cliquer sur Options avancées et vérifiez vos options de chiffrement.

    Si vous souhaitez tester le contenu chiffré de FairPlay, utilisez [ce lecteur test](http://aka.ms/amtest). Le lecteur prend en charge les DRM Widevine, PlayReady et FairPlay, ainsi que le chiffrement de clé en clair AES-128. Vous devez choisir le bon navigateur pour tester les différents DRM : Chrome/Opera/Firefox pour Widevine, MS Edge/IE11 pour PlayReady, Safari sur macOS pour FairPlay.

3. Secure Token Service (STS), qui émet le jeton JSON Web Token (JWT) en tant que jeton d’accès pour accéder aux ressources principales. Vous pouvez utiliser les services de distribution de licence AMS en tant que ressource principale. Un service STS doit définir les éléments suivants :

  * L’émetteur et l’audience (ou l’étendue)
  * Les revendications, qui dépendent des exigences de l’activité concernant la protection du contenu
  * La vérification symétrique ou asymétrique pour vérifier la signature
  * La prise en charge de substitution de clé (si nécessaire)

    Vous pouvez utiliser [cet outil STS](https://openidconnectweb.azurewebsites.net/DRMTool/Jwt) pour tester STS, qui prend en charge les 3 types de clé de vérification : symétriques, asymétriques ou AAD avec substitution de clé. 

> [!NOTE]
> Nous recommandons fortement de tester chaque partie (décrite plus haut) entièrement et une par une, avant de passer à la partie suivante. Pour tester votre système de « protection de contenu », utilisez les outils spécifiés dans la liste ci-dessus.  

## <a name="streaming-protocols-and-encryption-types"></a>Types de protocoles de diffusion en continu et de chiffrement

Vous pouvez utiliser Media Services pour transmettre du contenu chiffré de manière dynamique avec le chiffrement à clé en clair AES ou DRM en utilisant PlayReady, Widevine ou Apple FairPlay. Actuellement, vous pouvez chiffrer les formats de diffusion en continu HLS (HTTP Live Streaming), MPEG DASH et Smooth Streaming. Chaque protocole prend en charge les méthodes de chiffrement suivantes :

|Protocole|Format de conteneur|Schéma de chiffrement|
|---|---|---|---|
|MPEG-DASH|Tous|AES|
||CSF(fmp4) |CENC (Widevine + PlayReady) |
||CMAF(fmp4)|CENC (Widevine + PlayReady)|
|HLS|Tous|AES|
||MPG2-TS |CBCS (FairPlay) |
||MPG2-TS |CENC (PlayReady) |
||CMAF(fmp4) |CENC (PlayReady) |
|Smooth Streaming|fMP4|AES|
||fMP4 | CENC (PlayReady) |

## <a name="dynamic-encryption"></a>Chiffrement dynamique

Dans Media Services v3, une clé de contenu est associée à StreamingLocator (consultez [cet exemple](protect-with-aes128.md)). Si vous utilisez le service de remise de clé Media Services, vous devez générer automatiquement la clé de contenu. Vous devez générer la clé de contenu vous-même si vous utilisez votre propre service de remise de clé, ou si vous avez besoin de gérer un scénario de haute disponibilité au cours duquel vous avez besoin d’avoir la même clé de contenu dans deux centres de données.

Lorsqu’un flux est demandé par un lecteur, Media Services utilise la clé spécifiée pour chiffrer dynamiquement votre contenu à l’aide du chiffrement de clé en clair AES ou DRM. Pour déchiffrer le flux, le lecteur demande la clé au service de remise des clés Media Services, ou au service de remise des clés que vous avez spécifié. Pour déterminer si l’utilisateur est autorisé ou non à obtenir la clé, le service évalue la stratégie de clé de contenu que vous avez spécifiée pour la clé.

## <a name="aes-128-clear-key-vs-drm"></a>Clé en clair AES-128 et DRM

Les clients se demandent souvent s’ils devraient utiliser un chiffrement AES ou un système DRM. La principale différence entre les deux systèmes est qu’avec le chiffrement AES, la clé de contenu est transmise au client dans un format non chiffré (« en clair »). Par conséquent, la clé utilisée pour chiffrer le contenu peut être affichée dans la trace réseau du client en texte en clair. Le chiffrement avec une clé en clair AES-128 convient à des cas d’utilisation dans lesquels l’observateur est fiable (par exemple, le chiffrement de vidéos d’entreprise distribuées dans une société et destinées aux employés).

PlayReady, Widevine et FairPlay offrent tous un haut niveau de chiffrement comparé au chiffrement à clé en clair AES-128. La clé de contenu est transmise dans un format chiffré. De plus, le déchiffrement est pris en charge dans un environnement sécurisé au niveau du système d’exploitation, où il est plus difficile pour un utilisateur malintentionné de commettre une attaque. DRM est recommandé dans des cas d’utilisation où l’observateur peut ne pas être fiable et où donc un niveau de sécurité supérieur est requis.

## <a name="storage-side-encryption"></a>Chiffrement côté stockage

Pour protéger vos éléments au repos, les ressources doivent être chiffrées par le chiffrement côté stockage. Le tableau suivant montre comment fonctionne le chiffrement côté stockage dans Media Services v3 :

|Option de chiffrement|Description|Media Services v3|
|---|---|---|---|
|Chiffrement du stockage de Media Services| Chiffrement AES-256, clé gérée par Media Services|Non pris en charge<sup>(1)</sup>|
|[Storage Service Encryption pour les données au repos](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Chiffrement côté serveur proposé par le stockage Azure, clé gérée par Azure ou par un client|Pris en charge|
|[Chiffrement de stockage côté client](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Chiffrement côté client proposé par le stockage Azure, clé gérée par un client dans un coffre de clés|Non pris en charge|

<sup>1</sup> Dans Media Services v3, le chiffrement de stockage (chiffrement AES-256) est uniquement pris en charge pour la compatibilité descendante lorsque vos ressources ont été créées avec Media Services v2. Cela signifie que la version v3 fonctionne avec les ressources chiffrées du stockage existant mais qu’elle n’autorisera pas de nouvelles créations.

## <a name="licenses-and-keys-delivery-service"></a>Service de remise de licences et de clés

Media Services fournit un service de remise de licences DRM (PlayReady, Widevine, FairPlay) et de clés AES aux clients autorisés. Vous pouvez utiliser l’API REST ou le la bibliothèque de client Media Services pour configurer des stratégies d’authentification et d’autorisation pour vos licences et vos clés.

## <a name="control-content-access"></a>Contrôle de l’accès au contenu

Vous pouvez contrôler qui a accès à votre contenu en configurant la stratégie d’autorisation de clé de contenu. Media Services prend en charge plusieurs méthodes d’authentification des utilisateurs effectuant des demandes de clé. Vous devez configurer la stratégie de clé de contenu. Le client (lecteur) doit répondre à la stratégie avant que la clé de contenu ne puisse lui être remise. La stratégie de clé de contenu peut contenir une restriction **ouverte** ou **de jeton**. 

Avec une stratégie de clé de contenu de jeton, la clé de contenu n’est envoyée qu’à un client qui présente un JSON Web Token (JWT) ou Simple Web Token (SWT) valide dans la requête de clé/licence. Ce jeton doit être émis par un service d’émission de jeton de sécurité (STS). Vous pouvez utiliser Azure Active Directory en tant que STS ou déployer un STS personnalisé. Le STS doit être configuré pour créer un jeton signé avec la clé spécifiée et émettre les revendications spécifiées dans la configuration de restriction de jeton. Le service de remise de clé Media Services retourne la clé/licence demandée au client si le jeton est valide et que les revendications du jeton correspondent à celles configurées pour la clé/licence.

Quand vous configurez la stratégie de restriction par jeton, vous devez définir les paramètres de clé de vérification, émetteur et audience principaux. La clé de vérification principale contient le jeton avec lequel la clé a été signée. L’émetteur est le service STS qui émet le jeton. L’audience, parfois appelé étendue, décrit l’objectif du jeton ou la ressource à laquelle le jeton autorise l’accès. Le service de remise de clé Media Services valide le fait que les valeurs du jeton correspondent aux valeurs du modèle.

## <a name="a-idfaqfrequently-asked-questions"></a><a id="faq"/>Forum Aux Questions (FAQ)

### <a name="question"></a>Question

Comment implémenter un système multi-DRM (PlayReady, Widevine et FairPlay) à l’aide d’Azure Media Services (AMS) v3 tout en utilisant le service AMS de livraison de licences/clés ?

### <a name="answer"></a>Réponse

Pour découvrir un scénario de bout en bout, consultez [l’exemple de code suivant](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs). 

Cet exemple indique comment effectuer les opérations suivantes :

1. Créer et configurer ContentKeyPolicies.

  L’exemple contient des fonctions qui configurent des licences [PlayReady](playready-license-template-overview.md), [Widevine](widevine-license-template-overview.md) et [FairPlay](fairplay-license-overview.md).

    ```
    ContentKeyPolicyPlayReadyConfiguration playReadyConfig = ConfigurePlayReadyLicenseTemplate();
    ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
    ContentKeyPolicyFairPlayConfiguration fairPlayConfig = ConfigureFairPlayPolicyOptions();
    ```

2. Créer un élément StreamingLocator configuré pour diffuser en continu un élément multimédia chiffré. 

  Par exemple, vous pouvez définir StreamingLocator.StreamingPolicyName sur la stratégie « Predefined_MultiDrmCencStreaming ». Cette stratégie indique que vous souhaitez que deux clés de contenu (enveloppe et CENC) soient générées et définies sur le localisateur. Par conséquent, les chiffrements d’enveloppe, de PlayReady et de Widevine sont appliqués (la clé est envoyée au client de la lecture en fonction des licences DRM configurées). Si vous voulez aussi chiffrer votre flux avec CBCS (FairPlay), utilisez l’élément «Predefined_MultiDrmStreaming».

3. Créer un jeton de test.

  La méthode **GetTokenAsync** indique comment créer un jeton de test.
  
4. Générer l’URL de diffusion en continu.

  La méthode **GetDASHStreamingUrlAsync** indique comment générer l’URL de diffusion en continu. Dans le cas présent, l’URL diffuse en continu le flux **DASH**.

### <a name="question"></a>Question

Comment et où obtenir un jeton JWT avant de l’utiliser pour demander une licence ou une clé ?

### <a name="answer"></a>Réponse

1. Pour un environnement de production, vous devez disposer d’un service d’émission de jeton de sécurité (STS) (service web) qui émet un jeton JWT en réponse à une requête HTTPS. Dans le cas d’un environnement de test, vous pouvez utiliser le code figurant dans la méthode **GetTokenAsync** définie dans [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs).
2. Après l’authentification d’un utilisateur, le lecteur doit adresser une requête au service STS afin d’obtenir ce jeton et l’attribuer comme valeur de jeton. Vous pouvez utiliser [l’API Lecteur multimédia Azure](https://amp.azure.net/libs/amp/latest/docs/).

* Pour obtenir un exemple d’exécution de service STS, avec une clé symétrique ou asymétrique, accédez à l’adresse [http://aka.ms/jwt](http://aka.ms/jwt). 
* Pour découvrir un exemple de lecteur basé sur le Lecteur multimédia Azure utilisant ce type de jeton JWT, accédez à l’adresse [http://aka.ms/amtest](http://aka.ms/amtest) (développez le lien « player_settings » pour voir l’entrée de jeton).

### <a name="question"></a>Question

Comment autoriser les requêtes de diffusion en continu de vidéos avec chiffrement AES ?

### <a name="answer"></a>Réponse

L’approche correcte consiste à tirer profit du service STS :

Dans STS, en fonction du profil utilisateur, ajoutez différentes revendications (par exemple, « Utilisateur Premium », « Utilisateur de la version de base », « Utilisateur de la version d’évaluation gratuite »). La définition de différentes revendications dans un jeton JWT permet à l’utilisateur de visualiser des contenus distincts. Bien entendu, dans le cas d’un contenu/élément multimédia différent, l’élément ContentKeyPolicyRestriction disposera des RequiredClaims correspondants.

Utilisez l’API Azure Media Services pour la configuration de la livraison de licences/clés et pour le chiffrement de vos éléments multimédias (comme indiqué dans [cet exemple](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs)).

## <a name="next-steps"></a>Étapes suivantes

Consultez les articles suivants :

  * [Protection avec le chiffrement AES](protect-with-aes128.md)
  * [Protéger avec DRM](protect-with-drm.md)

Vous trouverez des informations supplémentaires dans [Concevoir un système de protection de contenu multi-DRM avec contrôle d’accès](design-multi-drm-system-with-access-control.md).


