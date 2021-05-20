---
title: Diffuser en direct avec Media Services en utilisant .NET Core
titleSuffix: Azure Media Services
description: Découvrez comment diffuser des événements en direct en utilisant .NET Core.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 06/13/2019
ms.author: inhenkel
ms.openlocfilehash: 504378dac7d868dd8c3fcdb5be778aa25c9766bf
ms.sourcegitcommit: 5da0bf89a039290326033f2aff26249bcac1fe17
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2021
ms.locfileid: "109713195"
---
# <a name="tutorial-stream-live-with-media-services-by-using-net-core"></a>Tutoriel : Diffuser en direct avec Media Services en utilisant .NET Core

Dans Azure Media Services, les [événements en direct](/rest/api/media/liveevents) sont responsables du traitement de contenu de streaming en direct. Un événement en direct fournit un point de terminaison d’entrée (URL d’ingestion) que vous fournissez ensuite à un encodeur live. Il reçoit des flux d’entrée d’un encodeur live et les rend disponibles pour le streaming par le biais d’un ou de plusieurs [points de terminaison de streaming](/rest/api/media/streamingendpoints). Les événements en direct fournissent également un point de terminaison d’aperçu (URL d’aperçu) que vous utilisez pour prévisualiser et valider votre flux avant de poursuivre le traitement ou la distribution. 

Ce didacticiel explique comment utiliser .NET Core pour créer un type de *canal direct* d’un événement en temps réel. Ce didacticiel présente les procédures suivantes :

> [!div class="checklist"]
> * Télécharger un exemple d’application
> * Examiner le code qui effectue la diffusion en continu
> * Regardez l’événement avec le [Lecteur multimédia Azure](https://amp.azure.net/libs/amp/latest/docs/index.html) sur le [site de démonstration du Lecteur multimédia](https://ampdemo.azureedge.net).
> * Supprimer des ressources.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> Bien que ce didacticiel utilise des exemples [.NET SDK](/dotnet/api/microsoft.azure.management.media.models.liveevent), les étapes générales sont les mêmes pour [l’API REST](/rest/api/media/liveevents), [l’interface de ligne de commande](/cli/azure/ams/live-event) et d’autres [kits de développement logiciel (SDK)](media-services-apis-overview.md#sdks) pris en charge. 

## <a name="prerequisites"></a>Prérequis

Pour ce tutoriel, vous devez disposer des éléments suivants :

- Installez Visual Studio Code ou Visual Studio.
- [Créer un compte Media Services](./account-create-how-to.md). Veillez à copier les détails de l’**accès à l’API** au format JSON ou à stocker les valeurs nécessaires pour se connecter au compte Media Services dans le format de fichier *.env* utilisé dans cet exemple.
- Suivez les étapes décrites dans [Accéder à l’API Azure Media Services avec Azure CLI](./access-api-howto.md) et enregistrez les informations d’identification. Vous devez les utiliser pour accéder à l’API dans cet exemple ou les entrer dans le format de fichier *.env*. 

Vous avez besoin de ces éléments supplémentaires pour les logiciels de streaming en direct :

- Une caméra ou appareil (tel qu’un ordinateur portable) utilisé pour diffuser un événement.
- Un encodeur logiciel local qui encode votre flux de caméra et l’envoie au service de streaming en direct Media Services via le protocole RTMP (Real-Time Messaging Protocol). Pour plus d’informations, consultez [Encodeurs live locaux recommandés](encode-recommended-on-premises-live-encoders.md). La diffusion doit se faire au format RTMP ou Smooth Streaming.  

  Cet exemple part du principe que vous allez utiliser Open Broadcaster Software (OBS) Studio pour diffuser le RTMP au point de terminaison d’ingestion. [Installez OBS Studio](https://obsproject.com/download). 

> [!TIP]
> Consultez [Streaming en direct avec Media Services v3](stream-live-streaming-concept.md) avant de commencer. 

## <a name="download-and-configure-the-sample"></a>Télécharger et configurer l’exemple

Clonez le dépôt GitHub qui contient l’exemple .NET de streaming en direct sur votre machine en utilisant la commande suivante :  

```bash
git clone https://github.com/Azure-Samples/media-services-v3-dotnet.git
```

L’exemple de streaming en direct se trouve dans le dossier [Live](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Live).

Ouvrez [appsettings.json](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/Live/LiveEventWithDVR/appsettings.json) dans votre projet téléchargé. Remplacez les valeurs par les informations d’identification que vous avez obtenues dans [Accéder à l’API Azure Media Services avec Azure CLI](./access-api-howto.md).

Notez que vous pouvez également utiliser le format de fichier *.env* à la racine du projet afin de définir vos variables d’environnement une seule fois pour tous les projets du dépôt d’exemples .NET. Il vous suffit de copier le fichier *sample.env*, puis de renseigner les informations que vous avez obtenues à partir de la page **Accéder à l’API** de Media Services dans le portail Azure ou à partir d’Azure CLI. Renommez le fichier *sample.env* en *.env* pour l’utiliser dans tous les projets.

Le fichier *.gitignore* est déjà configuré pour empêcher la publication de ce fichier dans votre dépôt dupliqué. 

> [!IMPORTANT]
> Cet exemple utilise un suffixe unique pour chaque ressource. Si vous annulez le débogage ou vous fermez l’application sans l’avoir effectué, vous obtiendrez plusieurs événements en direct sur votre compte.
>
> Veillez à arrêter les événements en direct en cours d’exécution. Sinon, *vous serez facturé* !

## <a name="examine-the-code-that-performs-live-streaming"></a>Examiner le code qui effectue la diffusion en continu

Cette section examine les fonctions définies dans le fichier [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/Live/LiveEventWithDVR/Program.cs) du projet *LiveEventWithDVR*.

L’exemple crée un suffixe unique pour chaque ressource afin d’éviter tout conflit de noms si vous exécutez l’exemple plusieurs fois sans le supprimer.


### <a name="start-using-media-services-apis-with-the-net-sdk"></a>Commencer à utiliser les API Media Services avec le SDK .NET

Pour commencer à utiliser les API Media Services avec .NET, vous devez créer un objet `AzureMediaServicesClient`. Pour créer l’objet, vous devez fournir des informations d’identification pour que le client puisse se connecter à Azure avec Azure Active Directory. Dans le code que vous avez cloné au début de l’article, la fonction `GetCredentialsAsync`crée l’objet `ServiceClientCredentials` en fonction des informations d’identification fournies dans le fichier de configuration local (*appsettings.json*) ou par le biais du fichier de variables d’environnement *.env* à la racine du dépôt.

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CreateMediaServicesClient)]

### <a name="create-a-live-event"></a>Créer un événement en temps réel

Cette section montre comment créer un type de *Pass-through* d’événement en direct (`LiveEventEncodingType` défini sur `None`). Pour plus d’informations sur les types disponibles, consultez [Types d’événements en direct](live-event-outputs-concept.md#live-event-types). En plus de Pass-through, vous pouvez utiliser un événement de transcodage live pour un encodage cloud à débit adaptatif 720p ou 1080p.
 
Vous souhaiterez peut-être spécifier les éléments suivants lors de la création de l’événement en direct :

* **Protocole d’ingestion pour l’événement en direct**. Les protocoles RTMP, RTMPS et Smooth Streaming sont pris en charge. Vous ne pouvez pas changer l’option de protocole pendant l’exécution de l’événement en direct ou des sorties en direct qui lui sont associées. Si vous avez besoin d’autres protocoles, créez des événements en direct distincts pour chaque protocole de streaming. 
* **Restrictions IP sur l’ingestion et l’aperçu**. Vous pouvez définir les adresses IP autorisées à recevoir du contenu vidéo sur cet événement en direct. Les adresses IP autorisées peuvent être spécifiées sous l’une des formes suivantes :

  * Une adresse IP unique (par exemple, `10.0.0.1`)
  * Plage d’adresses IP qui utilise une adresse IP et un masque de sous-réseau de routage CIDR (Classless InterDomain Routing) (par exemple, `10.0.0.1/22`)
  * Plage d’adresses IP utilisant une adresse IP et un masque de sous-réseau décimal séparé par des points (par exemple, `10.0.0.1(255.255.252.0)`)

  Si aucune adresse IP n’est spécifiée et qu’il n’existe pas de définition de règle, alors aucune adresse IP ne sera autorisée. Pour autoriser toutes les adresses IP, créez une règle et définissez la valeur `0.0.0.0/0`. Les adresses IP doivent utiliser un des formats suivants : adresses IPv4 à quatre chiffres ou une plage d’adresses CIDR.  
* **Démarrage automatique d’un événement quand vous le créez**. Quand le démarrage automatique est défini sur `true`, l’événement en direct démarre après sa création. La facturation commence donc dès le démarrage de l’événement en direct. Vous devez appeler explicitement la commande `Stop` sur la ressource de l’événement en direct pour arrêter toute facturation supplémentaire. Pour plus d’informations, consultez [États et facturation des événements en direct](live-event-states-billing-concept.md).

  Des modes veille permettent de démarrer l’événement en direct dans un état « alloué » à moindre coût qui accélère le passage à l’état En cours d’exécution. C’est utile dans certains, par exemple quand des pools très sollicités doivent distribuer rapidement des canaux aux diffuseurs.
* **Un nom d’hôte statique et un GUID unique**. Pour qu’une URL d’ingestion soit prévisible et plus facile à gérer dans un encodeur live matériel, affectez la valeur `true` à la propriété `useStaticHostname`. Pour plus d’informations, consultez [URL d’ingestion des événements en direct](live-event-outputs-concept.md#live-event-ingest-urls).

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CreateLiveEvent)]

### <a name="get-ingest-urls"></a>Obtenir les URL de réception

Après la création de l’événement en direct, vous pouvez obtenir des URL d’ingestion que vous fournirez à l’encodeur live. L’encodeur utilise ces URL pour entrer un flux dynamique.

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#GetIngestURL)]

### <a name="get-the-preview-url"></a>Obtenir l’URL d’aperçu

Utilisez `previewEndpoint` pour obtenir un aperçu et vérifier que l’entrée de l’encodeur est bien reçue.

> [!IMPORTANT]
> Avant de poursuivre, veillez à ce que la vidéo transite par l’URL d’aperçu.

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#GetPreviewURLs)]

### <a name="create-and-manage-live-events-and-live-outputs"></a>Créer et gérer des événements en direct et des sorties en direct

Une fois que le flux transite dans l’événement en direct, vous pouvez commencer l’événement de streaming en créant un actif multimédia, une sortie en direct et un localisateur de streaming. Le flux est alors archivé et mis à la disposition des observateurs via le point de terminaison de diffusion en continu.

Quand vous apprenez ces concepts, il est utile de penser à l’objet d’actif multimédia comme si c’était la cassette que vous insériez dans un magnétoscope à l’époque. La sortie en direct est le magnétoscope. L’événement en direct est simplement le signal vidéo arrivant à l’arrière de l’appareil.

Vous créez d’abord le signal en créant l’événement en direct. Le signal n’est pas transmis tant que vous n’avez pas démarré cet événement en direct et que vous n’avez pas connecté votre encodeur à l’entrée.

La « cassette » peut être créée à tout moment. Il s’agit juste d’un actif multimédia vide que vous allez transmettre à l’objet de sortie en direct, le « magnétoscope » dans cette analogie.

Le « magnétoscope » peut également être créé à tout moment. Vous pouvez créer une sortie en direct avant de démarrer le flux du signal, ou après. Si vous avez besoin d’accélérer les opérations, il est parfois utile de créer la sortie avant de démarrer le flux du signal.

Pour arrêter le « magnétoscope », vous appelez `delete` sur `LiveOutput`. Cette action ne supprime pas le *contenu* de la « bande » (actif multimédia). L’actif multimédia est toujours conservé avec le contenu vidéo archivé tant que vous n’appelez pas `delete` explicitement sur l’actif multimédia lui-même. 

La section suivante vous guidera tout au long de la création de l’actif multimédia et de la sortie en direct.

#### <a name="create-an-asset"></a>Créer une ressource

Créez un actif multimédia pour la sortie en direct à utiliser. Dans notre analogie, il s’agit de la « cassette » sur laquelle nous enregistrons le signal vidéo en direct. Les visiteurs peuvent voir le contenu en direct ou à la demande à partir de cette cassette virtuelle.

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CreateAsset)]

#### <a name="create-a-live-output"></a>Créer une sortie en direct

Les sorties en direct démarrent quand elles sont créées et s’arrêtent quand elles sont supprimées.  Quand vous supprimez la sortie en direct, vous ne supprimez pas l’actif multimédia sous-jacent ou le contenu de celui-ci. Cela revient à éjecter la « cassette ». L’actif multimédia contenant l’enregistrement dure aussi longtemps que vous le souhaitez. Quand il est éjecté (c’est-à-dire, quand la sortie en direct est supprimée), il est immédiatement disponible pour un visionnage à la demande.

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CreateLiveOutput)]

#### <a name="create-a-streaming-locator"></a>Créer un localisateur de diffusion en continu

> [!NOTE]
> Une fois votre compte Media Services créé, un point de terminaison de streaming par défaut est ajouté à votre compte dans l’état Arrêté. Pour démarrer le streaming de votre contenu et tirer parti de l’[empaquetage dynamique](encode-dynamic-packaging-concept.md) et du chiffrement dynamique, le point de terminaison de streaming à partir duquel vous souhaitez diffuser du contenu doit se trouver à l’état En cours d’exécution.

Quand vous publiez l’actif multimédia en utilisant un localisateur de streaming, l’événement en direct (jusqu’à la longueur de la fenêtre DVR) reste visible jusqu’à l’expiration ou la suppression du localisateur de streaming, en fonction de ce qui se produit en premier. C’est comme cela que vous pouvez mettre l’enregistrement sur « cassette » virtuelle à la disposition de votre public pour le regarder en direct et à la demande. La même URL peut être utilisée pour regarder l’événement en direct, la fenêtre DVR ou l’actif multimédia à la demande quand l’enregistrement est terminé (quand la sortie en direct est supprimée).

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CreateStreamingLocator)]

```csharp

// Get the URL to stream the output
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

### <a name="clean-up-resources-in-your-media-services-account"></a>Supprimer les ressources de votre compte Media Services

Si vous avez terminé de diffuser en streaming les événements et que vous voulez nettoyer les ressources provisionnées précédemment, utilisez la procédure ci-dessous :

1. Arrêtez d’envoyer le flux à partir de l’encodeur.
1. Arrêtez l’événement en temps réel. Une fois l’événement en direct arrêté, aucun frais n’est encouru. Lorsque vous devez le redémarrer, il possède la même URL de réception. Vous n’avez donc pas besoin de reconfigurer votre encodeur.
1. Arrêtez votre point de terminaison de streaming, sauf si vous souhaitez continuer à fournir l’archive de votre événement en direct en tant que flux à la demande. Si l’événement en direct est à l’état Arrêté, aucun frais n’est encouru.

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CleanupLiveEventAndOutput)]

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CleanupLocatorAssetAndStreamingEndpoint)]

## <a name="watch-the-event"></a>Regarder l’événement

Pour regarder l’événement, copiez l’URL de streaming que vous avez obtenue quand vous avez exécuté le code qui permet de créer un localisateur de streaming. Vous pouvez utiliser le lecteur multimédia de votre choix. Le [Lecteur multimédia Azure](https://amp.azure.net/libs/amp/latest/docs/index.html) est disponible pour tester votre flux sur le [site de démonstration du Lecteur multimédia](https://ampdemo.azureedge.net).

Une fois arrêté, un événement en direct est automatiquement converti en contenu à la demande. Même après l’arrêt et la suppression de l’événement, les utilisateurs pourront lire votre contenu archivé en tant que vidéo à la demande tant que vous n’aurez pas supprimé l’élément multimédia. Vous ne pouvez pas supprimer un actif multimédia si un événement est en train de l’utiliser. Vous devez d’abord supprimer l’événement.

## <a name="clean-up-remaining-resources"></a>Nettoyer les ressources restantes

Si vous n’avez plus besoin des ressources de votre groupe de ressources, notamment les comptes de stockage et Media Services que vous avez créés pour ce tutoriel, supprimez le groupe de ressources que vous avez créé.

Exécutez la commande d’interface de ligne de commande ci-dessous :

```azurecli-interactive
az group delete --name amsResourceGroup
```

> [!IMPORTANT]
> Le fait de laisser l’événement en direct s’exécuter entraîne des frais. Sachez que si le projet ou le programme cesse de répondre ou est fermé pour une raison quelconque, l’événement en direct peut continuer de s’exécuter et ainsi entraîner des frais.

## <a name="ask-questions-give-feedback-get-updates"></a>Poser des questions, envoyer des commentaires, obtenir des mises à jour

Découvrez l’article [Communauté Azure Media Services](media-services-community.md) pour découvrir les différentes façons dont vous pouvez poser des questions, faire des commentaires et obtenir des mises à jour sur Media Services.

## <a name="next-steps"></a>Étapes suivantes

[Diffuser des fichiers](stream-files-tutorial-with-api.md)
 
