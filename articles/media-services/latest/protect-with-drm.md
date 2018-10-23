---
title: Utilisation du service de remise des licences de chiffrement dynamique DRM avec Azure Media Services | Microsoft Docs
description: Vous pouvez utiliser Azure Media Services pour diffuser vos flux chiffrés avec des licences Microsoft PlayReady, Google Widevine ou Apple FairPlay.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/16/2018
ms.author: juliako
ms.openlocfilehash: 2a8a00ab034016e7121e4601b3ff5a16d8c721ac
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49395071"
---
# <a name="use-drm-dynamic-encryption-and-license-delivery-service"></a>Utilisation du chiffrement dynamique DRM et du service de remise des licences

Azure Media Services vous permet de fournir des flux MPEG-DASH, Smooth Streaming et HLS (HTTP Live Streaming) protégés par [la gestion des droits numériques (DRM) PlayReady](https://www.microsoft.com/playready/overview/). Vous pouvez également utiliser Media Services pour diffuser des flux DASH chiffrés avec des licences DRM **Google Widevine**. PlayReady et Widevine sont chiffrés conformément à la spécification de chiffrement commun (ISO/IEC 23001-7 CENC). Media Services vous permet également de chiffrer votre contenu HLS avec **Apple FairPlay** (AES-128 CBC). 

De plus, Media Services fournit un service de remise des licences DRM PlayReady et Widevine. Lorsqu’un utilisateur demande un contenu DRM protégé, l’application de lecteur demande une licence du service de licence Media Services. Si l’application de lecteur est autorisée, le service de licence Media Services remet une licence au lecteur. Une licence contient la clé de déchiffrement qui peut être utilisée par le lecteur client pour déchiffrer et diffuser le contenu.

Cet article est basé sur l’exemple [Chiffrement avec DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM). Entre autres choses, l’exemple montre comment :

* créer un encodage de transformation qui utilise une présélection intégrée pour un encodage à débit adaptatif et ingère un fichier directement à partir d’une [URL HTTPS source](job-input-from-http-how-to.md) ;
* définir la clé de signature utilisée pour la vérification de votre jeton.
* Définissez la configuration requise (les restrictions) sur la stratégie de clé de contenu pour transmettre des clés avec la configuration spécifiée. 

    * Configuration 
    
        Dans cet exemple, les licences [PlayReady](playready-license-template-overview.md) et [Widevine](widevine-license-template-overview.md) sont configurées de sorte qu’elles peuvent être transmises par le service de remise des licences Media Services. Bien que cet exemple d’application ne configure pas la licence [FairPlay](fairplay-license-overview.md), il contient une méthode que vous pouvez utiliser pour configurer FairPlay. Si vous le souhaitez, vous pouvez ajouter une configuration FairPlay comme autre option.

    * Restriction

        L’application définit une restriction de type jeton JWT dans la stratégie.

* Créez un élément StreamingLocator pour la ressource spécifiée, avec le nom de stratégie de diffusion en continu spécifié. Dans ce cas, la stratégie prédéfinie est utilisée. Elle définit deux clés de contenu dans l’élément StreamingLocator : AES-128 (enveloppe) et CENC (PlayReady et Widevine).  
    
    Une fois l’élément StreamingLocator créé, la ressource de sortie est publiée et mise à la disposition des clients pour la lecture.

    > [!NOTE]
    > Assurez-vous que le point de terminaison StreamingEndpoint à partir duquel vous souhaitez diffuser du contenu se trouve dans l’état En cours d’exécution.

* Créez une URL, vers le lecteur multimédia Azure Media Player, qui inclut le manifeste DASH et le jeton PlayReady nécessaires pour lire le contenu chiffré par PlayReady. L’exemple définit l’expiration du jeton JWT sur 1 heure. 

    Vous pouvez ouvrir un navigateur et coller l’URL obtenue pour lancer la page de démonstration du lecteur multimédia Azure Media Player avec l’URL et le jeton que vous avez déjà renseignés.  

    ![protéger avec drm](./media/protect-with-drm/playready_encrypted_url.png)

> [!NOTE]
> Vous pouvez chiffrer chaque ressource avec plusieurs types de chiffrement (AES-128, PlayReady, Widevine, FairPlay). Consultez les [protocoles de diffusion en continu et les types de chiffrement](content-protection-overview.md#streaming-protocols-and-encryption-types) pour identifier la meilleure combinaison.

L’exemple décrit dans cet article génère le résultat suivant :

![protéger avec drm](./media/protect-with-drm/ams_player.png)

## <a name="prerequisites"></a>Prérequis

Les éléments suivants sont requis pour suivre le didacticiel.

* Consultez l’article [Présentation de la protection du contenu](content-protection-overview.md).
* Lisez la section intitulée [Concevoir un système de protection de contenu multi-DRM avec contrôle d’accès](design-multi-drm-system-with-access-control.md).
* Installer Visual Studio Code ou Visual Studio
* Créez un nouveau compte Azure Media Services, comme décrit dans [ce démarrage rapide](create-account-cli-quickstart.md).
* Obtenir les informations d’identification nécessaires pour utiliser les API Media Services en respectant l’[accès aux API](access-api-cli-how-to.md)
* Définissez les valeurs appropriées dans le fichier de configuration d’application (appsettings.json).

## <a name="download-code"></a>Télécharger le code

Clonez un dépôt GitHub qui contient l’exemple .NET complet abordé dans cet article sur votre machine avec la commande suivante :

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
L’exemple de « chiffrement avec DRM » se trouve dans le dossier [EncryptWithDRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM).

> [!NOTE]
> L’exemple crée des ressources uniques chaque fois que vous exécutez l’application. En règle générale, vous réutilisez les ressources existantes telles que les transformations et les stratégies (si une ressource existante a des configurations requises). 

## <a name="start-using-media-services-apis-with-net-sdk"></a>Commencer à utiliser les API Media Services avec le Kit de développement logiciel (SDK) .NET

Pour commencer à utiliser les API Media Services avec .NET, vous devez créer un objet **AzureMediaServicesClient**. Pour créer l’objet, vous devez fournir les informations d’identification nécessaires pour que le client puisse se connecter à Azure à l’aide d’Azure AD. Dans le code que vous avez cloné au début de l’article, la fonction **GetCredentialsAsync** crée l’objet ServiceClientCredentials basé sur les informations d’identification fournies dans le fichier de configuration local. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>Créer une ressource de sortie  

La [ressource](https://docs.microsoft.com/rest/api/media/assets) de sortie stocke le résultat de votre travail d’encodage.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateOutputAsset)]
 
## <a name="get-or-create-an-encoding-transform"></a>Obtenir ou créer un encodage de transformation

Lorsque vous créez une instance de [transformation](https://docs.microsoft.com/rest/api/media/transforms), vous devez spécifier ce qu’elle doit produire comme sortie. Le paramètre requis est un objet **TransformOutput**, comme indiqué dans le code ci-dessous. Chaque objet **TransformOutput** contient un **préréglage**. Le **préréglage** décrit les instructions détaillées concernant les opérations de traitement vidéo et/ou audio qui doivent être utilisées pour générer l’objet **TransformOutput** souhaité. L’exemple décrit dans cet article utilise un préréglage appelé **AdaptiveStreaming**. Le préréglage encode la vidéo d’entrée dans une échelle des vitesses de transmission générée automatiquement (paires vitesse de transmission-résolution) basée sur la vitesse de transmission et la résolution de la sortie, et crée des fichiers MP4 ISO avec des fichiers audio AAC et des fichiers vidéo H.264 qui correspondent à chaque paire vitesse de transmission-résolution. 

Avant de créer une [transformation](https://docs.microsoft.com/rest/api/media/transforms), vous devez tout d’abord vérifier s’il en existe déjà une à l’aide de la méthode **Get**, comme indiqué dans le code suivant.  Dans Media Services v3, les méthodes **Get** appliquées sur les entités renvoient **null** si l’entité n’existe pas (une vérification du nom respectant la casse).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>Envoi du travail

Comme indiqué ci-dessus, l’objet [Transformation](https://docs.microsoft.com/rest/api/media/transforms) est la formule et un [travail](https://docs.microsoft.com/rest/api/media/jobs) est la requête réelle envoyée à Media Services pour appliquer cette **transformation** à un contenu vidéo ou audio d’entrée donné. Le **travail** spécifie des informations telles que l’emplacement de la vidéo d’entrée et celui de la sortie.

Dans ce tutoriel, nous créons l’entrée du travail à partir d’un fichier qui est ingéré directement depuis une [URL HTTPS source](job-input-from-http-how-to.md).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Attendre la fin du travail

Le travail prend du temps à se terminer et vous voulez être prévenu lorsque c’est le cas. L’exemple de code ci-dessous montre comment interroger le service pour connaître l’état du [travail](https://docs.microsoft.com/rest/api/media/jobs). L’interrogation n’est pas une meilleure pratique recommandée pour les applications de production en raison de la latence potentielle. L’interrogation peut être limitée si elle est utilisée de façon excessive sur un compte. À la place, les développeurs doivent utiliser Event Grid. Consultez [Acheminer des événements Azure Media Services vers un point de terminaison personnalisé à l’aide de CLI](job-state-events-cli-how-to.md).

Le **travail** passe généralement par les états suivants : **Planifié**, **En attente**,  **Traitement en cours**, **Terminé** (l’état final). Si le travail a rencontré une erreur, vous obtenez l’état **Erreur**. Si le travail est en cours d’annulation, vous obtenez **Annulation en cours** et **Annulé** une fois l’opération terminée.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#WaitForJobToFinish)]

## <a name="create-a-contentkeypolicy"></a>Créer une stratégie ContentKeyPolicy

Une clé de contenu fournit un accès sécurisé à vos éléments multimédias. Vous devez créer une stratégie de clé de contenu qui configure le mode de remise de la clé de contenu pour les clients finaux. La clé de contenu est associée à StreamingLocator. Media Services assure également le service de distribution des clés qui fournit des clés de chiffrement et des licences aux utilisateurs autorisés. 

Vous devez définir la configuration requise (les restrictions) sur la stratégie de clé de contenu pour transmettre des clés avec la configuration spécifiée. Dans cet exemple, nous avons défini les configurations et les conditions requises suivantes :

* Configuration 

    Les licences [PlayReady](playready-license-template-overview.md) et [Widevine](widevine-license-template-overview.md) sont configurées de sorte qu’elles peuvent être transmises par le service de remise des licences Media Services. Bien que cet exemple d’application ne configure pas la licence [FairPlay](fairplay-license-overview.md), il contient une méthode que vous pouvez utiliser pour configurer FairPlay. Vous pouvez ajouter une configuration FairPlay comme autre option.

* Restriction

    L’application définit une restriction de type jeton JWT dans la stratégie.

Lorsqu’un lecteur demande un flux de données, Media Services utilise la clé spécifiée pour chiffrer dynamiquement votre contenu. Pour déchiffrer le flux de données, le lecteur demande la clé au service de remise de clé. Pour déterminer si l’utilisateur est autorisé à obtenir la clé, le service évalue la stratégie de clé de contenu que vous avez spécifiée pour la clé.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaminglocator"></a>Créer un localisateur de diffusion en continu

Une fois l’encodage terminé, et la stratégie de clé de contenu configurée, l’étape suivante consiste à mettre à la disposition des clients la vidéo dans la ressource de sortie pour qu’ils puissent la lire. Vous pouvez réaliser cela en deux étapes : 

1. Créez un élément [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators).
2. Générez les URL de streaming que les clients peuvent utiliser. 

Le processus de création de l’élément **StreamingLocator** est appelé publication. Par défaut, l’élément **StreamingLocator** est valide immédiatement après avoir effectué les appels d’API et dure jusqu’à ce qu’il soit supprimé, sauf si vous configurez les durées de début et de fin optionnelles. 

Lors de la création d’un élément [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators), vous devez spécifier le nom **StreamingPolicyName** souhaité. Dans ce didacticiel, nous utilisons un des éléments StreamingPolicies prédéfinis, ce qui indique à Azure Media Services comment publier le contenu en vue de la diffusion en continu. Dans cet exemple, nous définissons l’élément StreamingLocator.StreamingPolicyName pour la stratégie «Predefined_MultiDrmCencStreaming». Cette stratégie indique que vous souhaitez que deux clés de contenu (enveloppe et CENC) soient générées et définies sur le localisateur. Par conséquent, les chiffrements d’enveloppe, de PlayReady et de Widevine sont appliqués (la clé est envoyée au client de la lecture en fonction des licences DRM configurées). Si vous souhaitez également chiffrer votre flux avec CBC (FairPlay), utilisez l’élément «Predefined_MultiDrmStreaming». 

> [!IMPORTANT]
> Lorsque vous utilisez une stratégie [StreamingPolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies) personnalisée, vous devez concevoir un ensemble limité de ces stratégies pour votre compte Media Services et les réutiliser pour vos éléments StreamingLocators chaque fois que les mêmes protocoles et options de chiffrement sont nécessaires. Votre compte Media Services a un quota en matière de nombre d’entrées de stratégie StreamingPolicy. Vous ne devez pas créer une stratégie StreamingPolicy pour chaque élément StreamingLocator.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>Obtenir un test de jeton
        
Dans ce didacticiel, nous spécifions que la stratégie de clé de contenu a une restriction au niveau du jeton. La stratégie de restriction à jeton doit être accompagnée d’un jeton émis par un service d’émission de jeton de sécurité (STS). Media Services prend en charge les jetons aux formats [JSON Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT), et c’est ce qui est configuré dans cet exemple.

L’élément ContentKeyIdentifierClaim est utilisé dans ContentKeyPolicy, ce qui signifie que le jeton présenté au service de remise de clé doit contenir l’identificateur de la ContentKey. Dans l’exemple, nous ne spécifions pas de clé de contenu lors de la création de l’élément StreamingLocator. Le système en crée une de manière aléatoire pour nous. Afin de générer le jeton de test, nous devons obtenir l’élément ContentKeyId à placer dans la revendication ContentKeyIdentifierClaim.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetToken)]

## <a name="build-a-dash-streaming-url"></a>Générer une URL de diffusion en continu DASH

Maintenant que l’élément [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) a été créé, vous pouvez obtenir les URL de diffusion en continu. Pour générer une URL, vous devez concaténer le nom d’hôte [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints) et le chemin d’accès **StreamingLocator**. Dans cet exemple, la *valeur par défaut* **StreamingEndpoint** est utilisée. Lorsque vous créez pour la première fois un compte Media Services, cette *valeur par défaut* **StreamingEndpoint** est à l’état Arrêté. Vous devez donc appeler **Démarrer**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetMPEGStreamingUrl)]

## <a name="clean-up-resources-in-your-media-services-account"></a>Supprimer les ressources de votre compte Media Services

En règle générale, vous devez supprimer tous les éléments à l’exception des objets que vous envisagez de réutiliser (habituellement, vous allez réutiliser les transformations et conserver les éléments StreamingLocators, etc.). Si vous souhaitez que votre compte soit propre après avoir effectué vos expériences, vous devez supprimer les ressources que vous n’envisagez pas de réutiliser.  Par exemple, le code suivant supprime les travaux.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CleanUp)]

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [protéger avec AES-128](protect-with-aes128.md)
