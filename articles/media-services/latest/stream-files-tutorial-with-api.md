---
title: Charger, encoder et diffuser en continu à l’aide d’Azure Media Services | Microsoft Docs
description: Suivez les étapes décrites dans ce didacticiel pour charger un fichier et encoder la vidéo, puis diffuser en continu votre contenu avec Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/09/2018
ms.author: juliako
ms.openlocfilehash: 7e5054d6f59bb3e06e4148bd9cfb3caed9fec970
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/14/2018
---
# <a name="tutorial-upload-encode-and-stream-videos-using-apis"></a>Didacticiel : Charger, encoder et diffuser en continu des vidéos à l’aide d’API

Ce didacticiel vous explique comment charger, encoder et diffuser en continu des fichiers vidéo avec Azure Media Services. Vous souhaitez pouvoir diffuser votre contenu aux formats CMAF, MPEG DASH ou HLS d’Apple, afin qu’il puisse être lu sur divers navigateurs et appareils. Votre vidéo doit être encodée et empaquetée correctement si vous souhaitez pouvoir la diffuser en continu.

Tandis que ce didacticiel vous guide tout au long des étapes nécessaires pour charger une vidéo, vous pouvez également encoder du contenu que vous mettez à la disposition de votre compte Media Services via une URL HTTPS.

![Lire la vidéo](./media/stream-files-tutorial-with-api/final-video.png)

Ce didacticiel vous explique les procédures suivantes :    

> [!div class="checklist"]
> * Lancement d’Azure Cloud Shell
> * Créer un compte Media Services
> * Accéder à l’API Media Services
> * Configurer l’exemple d’application
> * Examiner le code en détail
> * Exécution de l'application
> * Tester l’URL de diffusion en continu
> * Supprimer des ressources

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis


Si vous n’avez pas Visual Studio, vous pouvez obtenir [Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15).

## <a name="download-the-sample"></a>Téléchargez l’exemple

Clonez un référentiel GitHub qui contient l’exemple .NET de diffusion en continu sur votre machine à l’aide de la commande suivante :  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="examine-the-code"></a>Examiner le code

Cette section examine les fonctions définies dans le fichier [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs) du projet *UploadEncodeAndStreamFiles*.

### <a name="start-using-media-services-apis-with-net-sdk"></a>Commencer à utiliser les API Media Services avec le Kit de développement logiciel (SDK) .NET

Pour commencer à utiliser les API Media Services avec .NET, vous devez créer un objet **AzureMediaServicesClient**. Pour créer l’objet, vous devez fournir les informations d’identification nécessaires pour que le client puisse se connecter à Azure à l’aide d’Azure AD. Vous devez d’abord récupérer un jeton, puis créer un objet **ClientCredential** à partir du jeton renvoyé. Dans le code que vous avez cloné au début de l’article, l’objet **ArmClientCredential** est utilisé pour récupérer le jeton.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateMediaServicesClient)]

### <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>Créer une ressource d’entrée et charger un fichier local dans celle-ci 

La fonction **CreateInputAsset** crée une ressource d’entrée et charge le fichier vidéo local spécifié dans celle-ci. Cette ressource est utilisée en tant qu’entrée de votre travail d’encodage. Dans Media Services v3, l’entrée d’un travail peut être une ressource ou bien le contenu que vous mettez à la disposition de votre compte Media Services via des URL HTTPS. Si vous souhaitez savoir comment encoder à partir d’une URL HTTPS, consultez [cet](job-input-from-http-how-to.md) article.  

Dans Media Services v3, vous utilisez des API Stockage Azure pour charger des fichiers. L’extrait de code .NET suivant vous explique comment faire.

La fonction suivante effectue les actions ci-après :

* Elle crée une ressource 
* Elle récupère une [URL SAP](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) accessible en écriture vers le [conteneur de stockage](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet?tabs=windows#upload-blobs-to-the-container) de la ressource
* Elle charge le fichier dans le conteneur de stockage à l’aide de l’URL SAP

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

### <a name="create-an-output-asset-to-store-the-result-of-a-job"></a>Créer une ressource de sortie pour stocker le résultat d’un travail 

La ressource de sortie stocke le résultat de votre travail d’encodage. Le projet définit la fonction **DownloadResults** qui télécharge les résultats à partir de cette ressource de sortie dans le dossier « output », afin de voir ce que vous avez obtenu.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

### <a name="create-a-transform-and-a-job-that-encodes-the-uploaded-file"></a>Créer une transformation et un travail qui encode le fichier chargé
Lors de l’encodage ou du traitement de contenus dans Media Services, il est courant de configurer les paramètres d’encodage en tant que formule. Vous envoyez ensuite un **travail** pour appliquer cette formule à une vidéo. En envoyant de nouveaux travaux pour chaque nouvelle vidéo, vous appliquez cette formule à toutes les vidéos de votre bibliothèque. Une formule dans Media Services est référencée comme une **transformation**. Pour plus d’informations, consultez [Transforms and jobs](transform-concept.md) (Transformations et travaux). L’exemple décrit dans ce didacticiel définit une formule qui encode la vidéo, afin de la diffuser en continu sur divers appareils iOS et Android. 

#### <a name="transform"></a>Transformer

Lorsque vous créez une instance de **transformation**, vous devez spécifier ce qu’elle doit produire comme sortie. Le paramètre requis est un objet **TransformOutput**, comme indiqué dans le code ci-dessous. Chaque objet **TransformOutput** contient un **préréglage**. Le **préréglage** décrit les instructions détaillées concernant les opérations de traitement vidéo et/ou audio qui doivent être utilisées pour générer l’objet **TransformOutput** souhaité. L’exemple décrit dans cet article utilise un préréglage appelé **AdaptiveStreaming**. Le préréglage encode la vidéo d’entrée dans une échelle des vitesses de transmission générée automatiquement (paires vitesse de transmission-résolution) basée sur la vitesse de transmission et la résolution de la sortie, et crée des fichiers MP4 ISO avec des fichiers audio AAC et des fichiers vidéo H.264 qui correspondent à chaque paire vitesse de transmission-résolution. Pour plus d’informations sur ce préréglage, consultez [Encode with an auto-generated bitrate ladder](autogen-bitrate-ladder.md) (Encoder avec une échelle des vitesses de transmission générée automatiquement).

Vous pouvez utiliser d’autres préréglages EncoderNamedPreset intégrés ou des préréglages personnalisés. 

Lorsque vous créez une **transformation**, vous devez tout d’abord vérifier s’il en existe déjà une à l’aide de la méthode **Get**, comme indiqué dans le code qui suit.  Dans Media Services v3, les méthodes **Get** appliquées sur les entités renvoient **null** si l’entité n’existe pas (une vérification du nom respectant la casse).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#EnsureTransformExists)]

#### <a name="job"></a>Travail

Comme indiqué ci-dessus, l’objet **Transformation** est la formule et un **travail** est la requête réelle envoyée à Media Services pour appliquer cette **transformation** à un contenu vidéo ou audio d’entrée donné. Le **travail** spécifie des informations telles que l’emplacement de la vidéo d’entrée et celui de la sortie.

Dans cet exemple, la vidéo d’entrée a été chargée à partir de votre ordinateur local. Si vous souhaitez savoir comment encoder à partir d’une URL HTTPS, consultez [cet](job-input-from-http-how-to.md) article.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

### <a name="wait-for-the-job-to-complete"></a>Attendre la fin du travail

L’exemple de code ci-dessous montre comment interroger le service pour connaître l’état du travail. L’interrogation n’est pas une meilleure pratique recommandée pour les applications de production en raison de la latence potentielle. L’interrogation peut être limitée si elle est utilisée de façon excessive sur un compte. À la place, les développeurs doivent utiliser Event Grid.

Event Grid est conçu pour une haute disponibilité, des performances cohérentes et une mise à l’échelle dynamique. Avec Event Grid, vos applications peuvent écouter les événements de presque tous les services Azure ou de toute source personnalisée, et y réagir. La gestion simple et réactive des événements basée sur HTTP vous aide à générer des solutions efficaces grâce au filtrage et au routage intelligents des événements.  Consultez [Acheminer des événements Azure Media Services vers un point de terminaison personnalisé à l’aide de CLI](job-state-events-cli-how-to.md).

Le **travail** passe généralement par les états suivants : **Planifié**, **En attente**,  **Traitement en cours**, **Terminé** (l’état final). Si le travail a rencontré une erreur, vous obtenez l’état **Erreur**. Si le travail est en cours d’annulation, vous obtenez l’état **Annulation en cours** et l’état **Annulé** une fois l’opération terminée.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#WaitForJobToFinish)]

### <a name="get-a-streaminglocator"></a>Obtenir un élément StreamingLocator

Une fois l’encodage terminé, l’étape suivante consiste à mettre à la disposition des clients la vidéo dans la ressource de sortie pour qu’ils puissent la lire. Vous pouvez le faire en deux étapes : d’abord, créez un élément **StreamingLocator**, puis générez les URL de diffusion en continu que les clients peuvent utiliser. 

Le processus de création d’un élément **StreamingLocator** est appelée publication. Par défaut, l’élément **StreamingLocator** est valide immédiatement après avoir effectué les appels d’API et dure jusqu’à ce qu’il soit supprimé, sauf si vous configurez les durées de début et de fin optionnelles. 

Lors de la création d’un élément **StreamingLocator**, vous devez spécifier le **StreamingPolicyName** souhaité. Dans cet exemple, vous allez diffuser en continu du contenu en clair ou non chiffré ; la stratégie de diffusion en continu en clair prédéfinie **PredefinedStreamingPolicy.ClearStreamingOnly** peut donc être utilisée.

> [!IMPORTANT]
> Lorsque vous utilisez une stratégie StreamingPolicy personnalisée, vous devez concevoir un ensemble limité de ces stratégies pour votre compte Media Services et les réutiliser pour vos éléments StreamingLocators chaque fois que les mêmes protocoles et options de chiffrement sont nécessaires. Votre compte Media Services a un quota en matière de nombre d’entrées de stratégie StreamingPolicy. Vous ne devez pas créer une stratégie StreamingPolicy pour chaque élément StreamingLocator.

Le code suivant suppose que vous appelez la fonction avec un locatorName unique.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateStreamingLocator)]

Tandis que l’exemple de cette rubrique traite de la diffusion en continu, vous pouvez utiliser le même appel pour créer un élément StreamingLocator pour la diffusion vidéo via le téléchargement progressif.

### <a name="get-streaming-urls"></a>Obtenir des URL de diffusion en continu

Maintenant qu’un élément StreamingLocator a été créé, vous pouvez obtenir les URL de diffusion en continu, comme indiqué dans **GetStreamingURLs**. Pour générer une URL, vous devez concaténer le nom d’hôte **StreamingEndpoint** et le chemin d’accès **StreamingLocator**. Dans cet exemple, la *valeur par défaut* **StreamingEndpoint** est utilisée. Lorsque vous créez pour la première fois un compte Media Services, cette *valeur par défaut* **StreamingEndpoint** est à l’état Arrêté. Vous devez donc appeler **Démarrer**.

> [!NOTE]
> Dans cette méthode, vous avez besoin du locatorName qui a été utilisé lors de la création de l’élément **StreamingLocator** pour la ressource de sortie.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#GetStreamingURLs)]

### <a name="clean-up-resources-in-your-media-services-account"></a>Supprimer les ressources de votre compte Media Services

En règle générale, vous devez supprimer tous les éléments à l’exception des objets que vous envisagez de réutiliser (habituellement, vous allez réutiliser les transformations et conserver les éléments StreamingLocators, etc.). Si vous souhaitez que votre compte soit propre après avoir effectué vos expériences, vous devez supprimer les ressources que vous n’envisagez pas de réutiliser.  Par exemple, le code suivant supprime les travaux.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CleanUp)]

## <a name="run-the-sample-app"></a>Exécution de l'exemple d'application

1. Appuyez sur Ctrl + F5 pour exécuter l’application *EncodeAndStreamFiles*.
2. Copiez l’une des URL de diffusion en continu à partir de la console.

Cet exemple affiche les URL qui peuvent être utilisées pour lire la vidéo à l’aide de différents protocoles :

![Sortie](./media/stream-files-tutorial-with-api/output.png)

## <a name="test-the-streaming-url"></a>Tester l’URL de diffusion en continu

Pour tester la diffusion en continu, cet article utilise le lecteur multimédia Azure. 

> [!NOTE]
> Si un lecteur est hébergé sur un site https, veillez à mettre à jour l’URL vers « https ».

1. Ouvrez un navigateur web et accédez à [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. Dans le champ **URL :**, collez l’une des valeurs d’URL de diffusion en continu que vous avez obtenues lors de l’exécution de l’application. 
3. Appuyez sur **Mise à jour du Lecteur Windows Media**.

Le lecteur multimédia Azure peut être utilisé pour effectuer des tests, mais ne doit pas être utilisé dans un environnement de production. 

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous n’avez plus besoin des ressources de votre groupe de ressources, notamment les comptes de stockage et Media Services que vous avez créés pour ce didacticiel, supprimez le groupe de ressources créé précédemment. Vous pouvez utiliser l’outil **CloudShell**.

Dans **CloudShell**, exécutez la commande suivante :

```azurecli-interactive
az group delete --name amsResourceGroup
```

## <a name="multithreading"></a>Multithreading

Les Kits de développement logiciel (SDK) Azure Media Services v3 ne sont pas thread-safe. Lorsque vous développez une application multithread, vous devez générer et utiliser un nouvel objet AzureMediaServicesClient par thread.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous savez comment charger, encoder et diffuser en continu votre vidéo, consultez l’article suivant : 

> [!div class="nextstepaction"]
> [Analyser des vidéos](analyze-videos-tutorial-with-api.md)
