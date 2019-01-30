---
title: Diffuser en temps réel avec Azure Media Services v3 | Microsoft Docs
description: Ce didacticiel vous présente les étapes pour diffuser en temps réel avec Media Services v3 à l’aide de .NET Core.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/22/2019
ms.author: juliako
ms.openlocfilehash: c51a36f4380199de1ac62ef3f0c32bd0a8f06c01
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54811211"
---
# <a name="tutorial-stream-live-with-media-services-v3-using-apis"></a>Tutoriel : Diffuser en temps réel avec Media Services v3 à l’aide d’API

Dans Azure Media Services, les [événements en temps réel](https://docs.microsoft.com/rest/api/media/liveevents) sont responsables du traitement du contenu vidéo en flux continu. Un LiveEvent (événement en temps réel) fournit un point de terminaison d’entrée (URL de réception) que vous fournissez ensuite à un encodeur live. Il reçoit des flux de données entrants en continu d’un encodeur live et les rend disponibles en diffusion via un ou plusieurs [StreamingEndpoints](https://docs.microsoft.com/rest/api/media/streamingendpoints) (point de terminaison de streaming). Ces événements fournissent également un point de terminaison d’aperçu (URL d’aperçu) que vous utilisez pour obtenir un aperçu et valider votre flux avant tout traitement et remise supplémentaires. Ce didacticiel explique comment utiliser .NET Core pour créer un type de **canal direct** d’un événement en temps réel. 

> [!NOTE]
> Veillez à consulter [Diffusion en continu avec Media Services v3](live-streaming-overview.md) avant de commencer. 

Ce didacticiel explique les procédures suivantes :    

> [!div class="checklist"]
> * Accéder à l’API Media Services
> * Configurer l’exemple d’application
> * Examiner le code qui effectue la diffusion en continu
> * Surveiller l’événement avec [Lecteur multimédia Azure](http://amp.azure.net/libs/amp/latest/docs/index.html) à l’adresse http://ampdemo.azureedge.net
> * Supprimer des ressources

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

Les éléments suivants sont requis pour suivre le didacticiel.

- Installez Visual Studio Code ou Visual Studio.
- Installez et utilisez l’interface CLI localement. Vous devez disposer d’Azure CLI 2.0 ou version ultérieure pour cet article. Exécutez `az --version` pour trouver la version qui est à votre disposition. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI](/cli/azure/install-azure-cli). 

    Actuellement, les commandes [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref) ne fonctionnent pas toutes dans Azure Cloud Shell. Il est recommandé d’utiliser l’interface CLI localement.

- [Créer un compte Media Services](create-account-cli-how-to.md).

    Veillez à mémoriser les valeurs que vous avez utilisées pour le nom du groupe de ressources et le nom du compte Media Services

- Une caméra ou appareil (ordinateur portable) utilisé pour diffuser un événement.
- Un encodeur vidéo dynamique local qui convertit les signaux de la caméra en flux de données qui sont envoyés vers le service de vidéo en flux continu Media Services. La diffusion doit se faire au format **RTMP** ou **Smooth Streaming**.

## <a name="download-the-sample"></a>Télécharger l’exemple

Clonez un référentiel GitHub qui contient l’exemple .NET de diffusion en continu sur votre machine à l’aide de la commande suivante :  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```

L’exemple de diffusion en continu est situé dans le dossier [Live](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/Live/MediaV3LiveApp).

> [!IMPORTANT]
> Cet exemple utilise un suffixe unique pour chaque ressource. Si vous annulez le débogage ou fermer l’application sans l’avoir effectué, vous disposerez plusieurs événements en temps réel sur votre compte. <br/>
> Veillez à arrêter les événements en cours. Sinon, vous serez **facturé** !

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="examine-the-code-that-performs-live-streaming"></a>Examiner le code qui effectue la diffusion en continu

Cette section examine les fonctions définies dans le fichier [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs) du projet *MediaV3LiveApp*.

L’exemple crée un suffixe unique pour chaque ressource afin d’éviter tout conflit de noms si vous exécutez l’exemple plusieurs fois sans le supprimer.

> [!IMPORTANT]
> Cet exemple utilise un suffixe unique pour chaque ressource. Si vous annulez le débogage ou fermer l’application sans l’avoir effectué, vous disposerez plusieurs événements en temps réel sur votre compte. <br/>
> Veillez à arrêter les événements en cours. Sinon, vous serez **facturé** !
 
### <a name="start-using-media-services-apis-with-net-sdk"></a>Commencer à utiliser les API Media Services avec le Kit de développement logiciel (SDK) .NET

Pour commencer à utiliser les API Media Services avec .NET, vous devez créer un objet **AzureMediaServicesClient**. Pour créer l’objet, vous devez fournir les informations d’identification nécessaires pour que le client puisse se connecter à Azure à l’aide d’Azure AD. Dans le code que vous avez cloné au début de l’article, la fonction **GetCredentialsAsync** crée l’objet ServiceClientCredentials basé sur les informations d’identification fournies dans le fichier de configuration local. 

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateMediaServicesClient)]

### <a name="create-a-live-event"></a>Créer un événement en temps réel

Cette section montre comment créer un type de **canal direct** d’événement en temps réel (LiveEventEncodingType défini sur Aucun). Si vous voulez créer un événement en direct dont l’encodage live est activé, définissez LiveEventEncodingType sur **Standard**. 

Voici quelques éléments que vous voudrez probablement spécifier lors de la création de l’événement en temps réel :

* Emplacement Media Services 
* Le protocole de diffusion de l’événement en temps réel (les protocoles RTMP et Smooth Streaming sont actuellement pris en charge)
       
    Vous ne pouvez pas modifier l’option de protocole pendant l’exécution de l’événement ou de ses sorties directes associées. Si vous avez besoin d’autres protocoles, vous devez créer des événements en temps réel distincts pour chaque protocole de diffusion.  
* Restictions IP sur l’ingestion et la préversion. Vous pouvez définir les adresses IP autorisées à ingérer du contenu vidéo sur cet événement en temps réel. Les adresses IP autorisées peuvent être définies sous forme d’adresse IP unique (par exemple, « 10.0.0.1 »), de plage d’adresses IP constituée d’une adresse IP et d’un masque de sous-réseau CIDR (par exemple, « 10.0.0.1/22 ») ou de plage d’adresses IP constituée d’une adresse IP et d’un masque de sous-réseau au format décimal séparé par des points (par exemple, « 10.0.0.1(255.255.252.0) »).
    
    Si aucune adresse IP n’est spécifiée et qu’il n’existe pas de définition de règle, alors aucune adresse IP ne sera autorisée. Pour autoriser toutes les adresses IP, créez une règle et définissez la valeur 0.0.0.0/0.
    
    Les adresses IP doivent utiliser un des formats suivants : adresses IpV4 avec 4 nombres, plage d’adresses CIDR.

* Lors de la création de l’événement, vous pouvez préciser que vous voulez que le démarrage soit automatique. 

    Lorsque le démarrage automatique est défini sur true, l’événement en direct démarre après sa création. Cela signifie que la facturation commence dès que l’événement en direct est en cours d’exécution. Vous devez appeler explicitement la commande Stop sur la ressource de l’événement en remps réel pour arrêter toute facturation supplémentaire. Pour plus d’informations, consultez la section [États et facturation d’événements en temps réel](live-event-states-billing.md).

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateLiveEvent)]

### <a name="get-ingest-urls"></a>Obtenir les URL de réception

Une fois le LiveEvent créé, vous pouvez obtenir des URL de réception que vous devez fournir à l’encodeur live. L’encodeur utilise ces URL pour entrer un flux dynamique.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#GetIngestURL)]

### <a name="get-the-preview-url"></a>Obtenir l’URL d’aperçu

Utilisez le point de terminaison d’aperçu et vérifiez que l’entrée de l’encodeur est bien reçue.

> [!IMPORTANT]
> Veillez à ce que la vidéo transite par l’URL d’aperçu avant de poursuivre !

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#GetPreviewURLs)]

### <a name="create-and-manage-liveevents-and-liveoutputs"></a>Créer et gérer des événements et des sorties en temps réel

Une fois que le flux transite dans l’événement en temps réel, vous pouvez commencer l’événement de diffusion en continu en créant une ressource, une sortie en temps réel et un localisateur de diffusion en continu. Le flux est alors archivé et mis à la disposition des observateurs via le point de terminaison de diffusion en continu. 

#### <a name="create-an-asset"></a>Créer une ressource

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateAsset)]

Créez une ressource pour la sortie en temps réel à utiliser.

#### <a name="create-a-liveoutput"></a>Créer une sortie en temps réel

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateLiveOutput)]

#### <a name="create-a-streaminglocator"></a>Créer un localisateur de diffusion en continu

> [!NOTE]
> Une fois votre compte Media Services créé, un point de terminaison de streaming **par défaut** est ajouté à votre compte à l’état **Arrêté**. Pour démarrer la diffusion en continu de votre contenu et tirer parti de l’empaquetage et du chiffrement dynamiques, le point de terminaison de streaming à partir duquel vous souhaitez diffuser du contenu doit se trouver à l’état **En cours d’exécution**. 

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateStreamingLocator)]

```csharp

// Get the url to stream the output
ListPathsResponse paths = await client.StreamingLocators.ListPathsAsync(resourceGroupName, accountName, locatorName);

foreach (StreamingPath path in paths.StreamingPaths)
{
    UriBuilder uriBuilder = new UriBuilder();
    uriBuilder.Scheme = "https";
    uriBuilder.Host = streamingEndpoint.HostName;

    uriBuilder.Path = path.Paths[0];
    // Get the URL from the uriBuilder: uriBuilder.ToString()
}
```

### <a name="cleaning-up-resources-in-your-media-services-account"></a>Suppression des ressources de votre compte Media Services

Si vous avez terminé de diffuser en continu les événements et que vous voulez nettoyer les ressources configurées précédemment, suivez la procédure ci-dessous.

* Arrêtez d’envoyer le flux à partir de l’encodeur.
* Arrêtez l’événement en temps réel. Une fois l’événement arrêté, aucun frais n’est encouru. Lorsque vous devez le redémarrer, il possède la même URL de réception. Vous n’avez donc pas besoin de reconfigurer votre encodeur.
* Vous pouvez arrêter votre point de terminaison de diffusion en continu, sauf si vous souhaitez continuer à fournir l’archive de votre événement en direct en tant que flux à la demande. Si l’événement est arrêté, aucun frais n’est encouru.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupLiveEventAndOutput)]

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupLocatorAssetAndStreamingEndpoint)]

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupAccount)]   

## <a name="watch-the-event"></a>Regarder l’événement

Pour surveiller l’événement, copiez l’URL de diffusion obtenue lors de l’exécution du code décrit dans [Créer un localisateur de diffusion en continu](#create-a-streaminglocator) et utilisez le lecteur de votre choix. Vous pouvez utiliser le [Lecteur multimédia Azure](http://amp.azure.net/libs/amp/latest/docs/index.html) pour tester votre flux à l’adresse http://ampdemo.azureedge.net. 

Une fois arrêté, l’événement en direct est automatiquement converti en contenu à la demande. Même après l’arrêt et la suppression de l’événement, les utilisateurs pourront lire votre contenu archivé en tant que vidéo à la demande tant que vous n’aurez pas supprimé l’élément multimédia. Un élément multimédia ne peut pas être supprimé s’il est utilisé par un événement ; vous devez d’abord supprimer l’événement. 

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous n’avez plus besoin des ressources de votre groupe de ressources, notamment les comptes de stockage et Media Services que vous avez créés pour ce didacticiel, supprimez le groupe de ressources créé précédemment.

Exécutez la commande CLI suivante :

```azurecli-interactive
az group delete --name amsResourceGroup
```

> [!IMPORTANT]
> Laisser l’événement tourner entraîne des frais. N’oubliez pas : si le projet/programme cesse de fonctionner, ou s’il est fermé pour une quelconque raison, l’événement peut continuer de tourner et ainsi entraîner des frais.

## <a name="next-steps"></a>Étapes suivantes

[Diffuser des fichiers](stream-files-tutorial-with-api.md)

