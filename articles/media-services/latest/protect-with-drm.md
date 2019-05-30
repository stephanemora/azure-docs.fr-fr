---
title: Utiliser DRM dynamique chiffrement et la licence service de distribution avec Azure Media Services | Microsoft Docs
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
ms.topic: conceptual
ms.date: 05/25/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: ce3b7a29f6f57b2bc309c719dbbab6c4574f0a46
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66306487"
---
# <a name="tutorial-use-drm-dynamic-encryption-and-license-delivery-service"></a>Didacticiel : Utilisation du chiffrement dynamique DRM et du service de remise des licences

Vous pouvez utiliser Azure Media Services pour diffuser vos flux chiffrés avec des licences Microsoft PlayReady, Google Widevine ou Apple FairPlay. Pour une explication détaillée, consultez [protection avec le chiffrement dynamique du contenu](content-protection-overview.md).

De plus, Media Services fournit un service de remise des licences DRM PlayReady et Widevine. Lorsqu’un utilisateur demande un contenu DRM protégé, l’application de lecteur demande une licence du service de licence Media Services. Si l’application de lecteur est autorisée, le service de licence Media Services remet une licence au lecteur. Une licence contient la clé de déchiffrement qui peut être utilisée par le lecteur client pour déchiffrer et diffuser le contenu.

Cet article est basé sur l’exemple [Chiffrement avec DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM). 

L’exemple décrit dans cet article génère le résultat suivant :

![AMS avec la vidéo protégée par DRM](./media/protect-with-drm/ams_player.png)

Ce didacticiel vous explique les procédures suivantes :    

> [!div class="checklist"]
> * Créer une transformation de l’encodage
> * Définir la clé de signature utilisée pour la vérification de votre jeton
> * Définir les exigences sur la stratégie de clé de contenu
> * Créer un StreamingLocator avec la stratégie de diffusion en continu spécifiée
> * Créer une URL utilisée pour la lecture de votre fichier

## <a name="prerequisites"></a>Conditions préalables

Les éléments suivants sont requis pour suivre le didacticiel.

* Consultez l’article [Présentation de la protection du contenu](content-protection-overview.md).
* Examinez le [concevoir le système de protection du contenu de multi-DRM avec contrôle d’accès](design-multi-drm-system-with-access-control.md)
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

La [ressource](assets-concept.md) de sortie stocke le résultat de votre travail d’encodage.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateOutputAsset)]
 
## <a name="get-or-create-an-encoding-transform"></a>Obtenir ou créer un encodage de transformation

Lorsque vous créez une instance de [transformation](transforms-jobs-concept.md), vous devez spécifier ce qu’elle doit produire comme sortie. Le paramètre requis est un `transformOutput` de l’objet, comme indiqué dans le code ci-dessous. Chaque TransformOutput contient un **présélection**. Présélection décrit les instructions pas à pas d’opérations de traitement vidéo et/ou audio qui doivent être utilisées pour générer le TransformOutput souhaitée. L’exemple décrit dans cet article utilise un préréglage appelé **AdaptiveStreaming**. Le préréglage encode la vidéo d’entrée dans une échelle des vitesses de transmission générée automatiquement (paires vitesse de transmission-résolution) basée sur la vitesse de transmission et la résolution de la sortie, et crée des fichiers MP4 ISO avec des fichiers audio AAC et des fichiers vidéo H.264 qui correspondent à chaque paire vitesse de transmission-résolution. 

Avant de créer une **transformation**, vous devez tout d’abord vérifier s’il en existe déjà une à l’aide de la méthode **Get**, comme indiqué dans le code suivant.  Dans Media Services v3, les méthodes **Get** appliquées sur les entités renvoient **null** si l’entité n’existe pas (une vérification du nom respectant la casse).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>Envoi du travail

Comme indiqué ci-dessus, l’objet **Transformation** est la formule et un [travail](transforms-jobs-concept.md) est la requête réelle envoyée à Media Services pour appliquer cette **transformation** à un contenu vidéo ou audio d’entrée donné. Le **travail** spécifie des informations telles que l’emplacement de la vidéo d’entrée et celui de la sortie.

Dans ce tutoriel, nous créons l’entrée du travail à partir d’un fichier qui est ingéré directement depuis une [URL HTTPS source](job-input-from-http-how-to.md).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Attendre la fin du travail

Le travail prend du temps à se terminer et vous voulez être prévenu lorsque c’est le cas. L’exemple de code ci-dessous montre comment interroger le service pour connaître l’état du **travail**. L’interrogation n’est pas une meilleure pratique recommandée pour les applications de production en raison de la latence potentielle. L’interrogation peut être limitée si elle est utilisée de façon excessive sur un compte. À la place, les développeurs doivent utiliser Event Grid. Consultez [Acheminer des événements Azure Media Services vers un point de terminaison personnalisé à l’aide de CLI](job-state-events-cli-how-to.md).

Le **travail** passe généralement par les états suivants : **Planifié**, **En attente**,  **Traitement en cours**, **Terminé** (l’état final). Si le travail a rencontré une erreur, vous obtenez l’état **Erreur**. Si le travail est en cours d’annulation, vous obtenez **Annulation en cours** et **Annulé** une fois l’opération terminée.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#WaitForJobToFinish)]

## <a name="create-a-content-key-policy"></a>Créer une stratégie de clé de contenu

Une clé de contenu fournit un accès sécurisé à vos éléments multimédias. Vous devez créer un [stratégie de la clé de contenu](content-key-policy-concept.md) pour chiffrer votre contenu avec un DRM. La stratégie configure le mode de remise de la clé de contenu pour les clients. La clé de contenu est associée à un localisateur de diffusion en continu. Media Services assure également le service de distribution des clés qui fournit des clés de chiffrement et des licences aux utilisateurs autorisés. 

Vous devez définir la configuration requise (restrictions) sur le **stratégie clé de contenu** qui doivent être remplies pour fournir des clés avec la configuration spécifiée. Dans cet exemple, nous avons défini les configurations et les conditions requises suivantes :

* Configuration 

    Les licences [PlayReady](playready-license-template-overview.md) et [Widevine](widevine-license-template-overview.md) sont configurées de sorte qu’elles peuvent être transmises par le service de remise des licences Media Services. Bien que cet exemple d’application ne configure pas la licence [FairPlay](fairplay-license-overview.md), il contient une méthode que vous pouvez utiliser pour configurer FairPlay. Vous pouvez ajouter une configuration FairPlay comme autre option.

* Restriction

    L’application définit une restriction de type jeton JWT dans la stratégie.

Lorsqu’un lecteur demande un flux de données, Media Services utilise la clé spécifiée pour chiffrer dynamiquement votre contenu. Pour déchiffrer le flux de données, le lecteur demande la clé au service de remise de clé. Pour déterminer si l’utilisateur est autorisé à obtenir la clé, le service évalue la stratégie de clé de contenu que vous avez spécifiée pour la clé.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaming-locator"></a>Créer un localisateur de streaming

Une fois l’encodage terminé, et la stratégie de clé de contenu configurée, l’étape suivante consiste à mettre à la disposition des clients la vidéo dans la ressource de sortie pour qu’ils puissent la lire. Vous pouvez réaliser cela en deux étapes : 

1. Créer un [localisateur de streaming](streaming-locators-concept.md)
2. Générez les URL de streaming que les clients peuvent utiliser. 

Le processus de création du **localisateur de diffusion en continu** est appelée publication. Par défaut, le **localisateur de streaming** est valide immédiatement après avoir effectué les appels d’API et dure jusqu’à ce qu’il soit supprimé, sauf si vous configurez les durées de début et de fin optionnelles. 

Lorsque vous créez un **localisateur de diffusion en continu**, vous devez spécifier le texte souhaité `StreamingPolicyName`. Dans ce didacticiel, nous utilisons un de la diffusion en continu des stratégies prédéfinies, qui indique à Azure Media Services comment publier le contenu de diffusion en continu. Dans cet exemple, nous définissons l’élément StreamingLocator.StreamingPolicyName pour la stratégie «Predefined_MultiDrmCencStreaming». Le chiffrement PlayReady et Widevine est appliquées, la clé est envoyée au client la lecture basé sur les licences DRM configurés. Si vous voulez aussi chiffrer votre flux avec CBCS (FairPlay), utilisez l’élément «Predefined_MultiDrmStreaming». 

> [!IMPORTANT]
> Quand vous utilisez une [stratégie de streaming](streaming-policy-concept.md) personnalisée, vous devez concevoir un ensemble limité de ces stratégies pour votre compte Media Services et les réutiliser pour vos éléments StreamingLocators chaque fois que les mêmes protocoles et options de chiffrement sont nécessaires. Votre compte Media Services a un quota en matière de nombre d’entrées de stratégie StreamingPolicy. Vous ne devez pas créer une stratégie StreamingPolicy pour chaque élément StreamingLocator.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>Obtenir un test de jeton
        
Dans ce didacticiel, nous spécifions que la stratégie de clé de contenu a une restriction au niveau du jeton. La stratégie de restriction à jeton doit être accompagnée d’un jeton émis par un service d’émission de jeton de sécurité (STS). Media Services prend en charge les jetons aux formats [JSON Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT), et c’est ce qui est configuré dans cet exemple.

L’élément ContentKeyIdentifierClaim est utilisé dans ContentKeyPolicy, ce qui signifie que le jeton présenté au service de remise de clé doit contenir l’identificateur de la ContentKey. Dans l’exemple, nous ne spécifions pas une clé de contenu lors de la création du localisateur de diffusion en continu, le système crée un de manière aléatoire pour nous. Afin de générer le jeton de test, nous devons obtenir l’élément ContentKeyId à placer dans la revendication ContentKeyIdentifierClaim.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetToken)]

## <a name="build-a-streaming-url"></a>Générer une URL de diffusion en continu

Maintenant que l’élément [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) a été créé, vous pouvez obtenir les URL de diffusion en continu. Pour générer une URL, vous devez concaténer la [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints) nom d’hôte et le **localisateur de diffusion en continu** chemin d’accès. Dans cet exemple, le **point de terminaison de streaming** *par défaut* est utilisé. Quand vous créez pour la première fois un compte Media Services, ce **point de terminaison de streaming** *par défaut* est dans l’état Arrêté. Vous devez donc appeler **Start**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetMPEGStreamingUrl)]

Lorsque vous exécutez l’application, vous consultez les rubriques suivantes :

![Protéger avec DRM](./media/protect-with-drm/playready_encrypted_url.png)

Vous pouvez ouvrir un navigateur et coller l’URL obtenue pour lancer la page de démonstration du lecteur multimédia Azure Media Player avec l’URL et le jeton que vous avez déjà renseignés. 
 
## <a name="clean-up-resources-in-your-media-services-account"></a>Supprimer les ressources de votre compte Media Services

En règle générale, vous devez supprimer tous les éléments à l’exception des objets que vous envisagez de réutiliser (habituellement, vous allez réutiliser les transformations et conserver les éléments StreamingLocators, etc.). Si vous souhaitez que votre compte soit propre après avoir effectué vos expériences, vous devez supprimer les ressources que vous n’envisagez pas de réutiliser.  Par exemple, le code suivant supprime les travaux.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CleanUp)]

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous n’avez plus besoin des ressources de votre groupe de ressources, notamment les comptes de stockage et Media Services que vous avez créés pour ce didacticiel, supprimez le groupe de ressources créé précédemment. 

Exécutez la commande CLI suivante :

```azurecli
az group delete --name amsResourceGroup
```

## <a name="ask-questions-give-feedback-get-updates"></a>Poser des questions, envoyer des commentaires, obtenir des mises à jour

Découvrez l’article [Communauté Azure Media Services](media-services-community.md) pour découvrir les différentes façons dont vous pouvez poser des questions, faire des commentaires et obtenir des mises à jour sur Media Services.

## <a name="next-steps"></a>Étapes suivantes

Découvrez

> [!div class="nextstepaction"]
> [Protéger avec AES-128](protect-with-aes128.md)

