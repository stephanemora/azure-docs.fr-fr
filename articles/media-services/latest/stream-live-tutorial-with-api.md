---
titre : Diffuser en direct avec Media Services v3 : Description d’Azure Media Services : Découvrez comment utiliser Azure Media Services v3 pour diffuser en direct
services: media-services documentationcenter: '' author: IngridAtMicrosoft manager: femila editor: ''

ms.service: media-services ms.workload: media ms.tgt_pltfrm: na ms.devlang: na ms.topic: tutorial ms.custom: "mvc, devx-track-csharp" ms.date: 06/13/2019 ms.author: inhenkel

---

# <a name="tutorial-stream-live-with-media-services"></a>Tutoriel : Diffuser en direct avec Media Services

> [!NOTE]
> Bien que ce didacticiel utilise des exemples [.NET SDK](/dotnet/api/microsoft.azure.management.media.models.liveevent), les étapes générales sont les mêmes pour [l’API REST](/rest/api/media/liveevents), [l’interface de ligne de commande](/cli/azure/ams/live-event) et d’autres [kits de développement logiciel (SDK)](media-services-apis-overview.md#sdks) pris en charge. 

Dans Azure Media Services, les [événements en direct](/rest/api/media/liveevents) sont responsables du traitement de contenu de streaming en direct. Un événement en direct fournit un point de terminaison d’entrée (URL de réception) que vous fournissez ensuite à un encodeur live. Il reçoit des flux d’entrée en temps réel d’un encodeur live et le rend disponible pour le streaming par le biais d’un ou de plusieurs [points de terminaison de streaming](/rest/api/media/streamingendpoints). Les événements en direct fournissent également un point de terminaison d’aperçu (URL d’aperçu) que vous utilisez pour prévisualiser et valider votre flux avant de poursuivre le traitement ou la distribution. Ce didacticiel explique comment utiliser .NET Core pour créer un type de **canal direct** d’un événement en temps réel.

Ce didacticiel explique les procédures suivantes :

> [!div class="checklist"]
> * Télécharger l’exemple d’application décrit dans la rubrique.
> * Examiner le code qui effectue la diffusion en continu
> * Surveiller l’événement avec le [Lecteur multimédia Azure](https://amp.azure.net/libs/amp/latest/docs/index.html) à l’adresse [https://ampdemo.azureedge.net](https://ampdemo.azureedge.net).
> * Supprimer des ressources.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

Les éléments suivants sont requis pour suivre le didacticiel :

- Installez Visual Studio Code ou Visual Studio.
- [Créer un compte Media Services](./account-create-how-to.md).<br/>Veillez à copier les détails de l’accès à l’API au format JSON ou à stocker les valeurs nécessaires pour se connecter au compte Media Services dans le format de fichier .env utilisé dans cet exemple.
- Suivez les étapes décrites dans [Accéder à l’API Azure Media Services avec Azure CLI](./access-api-howto.md) et enregistrez les informations d’identification. Vous devez les utiliser pour accéder à l’API dans cet exemple ou les entrer dans le format de fichier .env. 
- Une caméra ou appareil (tel qu’un ordinateur portable) utilisé pour diffuser un événement.
- Un encodeur logiciel local qui encode votre flux de caméra et l’envoie au service de streaming en direct Media Services avec le protocole RTMP ; consultez les [encodeurs live locaux recommandés](encode-recommended-on-premises-live-encoders.md). La diffusion doit se faire au format **RTMP** ou **Smooth Streaming**.  
- Pour cet exemple, nous vous recommandons de commencer avec un encodeur logiciel comme le logiciel gratuit [Open Broadcast Software OBS Studio](https://obsproject.com/download). 

> [!TIP]
> Veillez à consulter [Diffusion en continu avec Media Services v3](stream-live-streaming-concept.md) avant de commencer. 

## <a name="download-and-configure-the-sample"></a>Télécharger et configurer l’exemple

Clonez le dépôt GitHub suivant qui contient l’exemple .NET de streaming en direct sur votre machine avec la commande suivante :  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet.git
 ```

L’exemple de diffusion en continu est situé dans le dossier [Live](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Live).

Ouvrez [appsettings.json](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/Live/LiveEventWithDVR/appsettings.json) dans votre projet téléchargé. Remplacez les valeurs par les informations d’identification que vous avez obtenues en [accédant aux API](./access-api-howto.md).

Notez que vous pouvez également utiliser le format de fichier .env à la racine du projet afin de définir vos variables d’environnement une seule fois pour tous les projets du dépôt d’exemples .NET. Il vous suffit de copier le fichier sample.env, de renseigner les informations que vous obtenez à partir d’Azure CLI ou de la page d’accès à l’API Media Services dans le portail Azure.  Renommez le fichier sample.env en « .env » pour l’utiliser dans tous les projets.
Le fichier .gitignore est déjà configuré pour éviter que le contenu de ce fichier soit publié dans votre dépôt dupliqué. 

> [!IMPORTANT]
> Cet exemple utilise un suffixe unique pour chaque ressource. Si vous annulez le débogage ou vous fermez l’application sans l’avoir effectué, vous obtiendrez plusieurs événements en direct sur votre compte. <br/>Veillez à arrêter les événements en direct en cours d’exécution. Sinon, vous serez **facturé** !

## <a name="examine-the-code-that-performs-live-streaming"></a>Examiner le code qui effectue la diffusion en continu

Cette section examine les fonctions définies dans le fichier [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/Live/LiveEventWithDVR/Program.cs) du projet *LiveEventWithDVR*.

L’exemple crée un suffixe unique pour chaque ressource afin d’éviter tout conflit de noms si vous exécutez l’exemple plusieurs fois sans le supprimer.


### <a name="start-using-media-services-apis-with-net-sdk"></a>Commencer à utiliser les API Media Services avec le Kit de développement logiciel (SDK) .NET

Pour commencer à utiliser les API Media Services avec .NET, vous devez créer un objet **AzureMediaServicesClient**. Pour créer l’objet, vous devez fournir les informations d’identification nécessaires pour que le client puisse se connecter à Azure à l’aide d’Azure AD. Dans le code que vous avez cloné au début de l’article, la fonction **GetCredentialsAsync** crée l’objet ServiceClientCredentials en fonction des informations d’identification fournies dans le fichier de configuration local (appsettings.json) ou par le biais du fichier de variables d’environnement .env situé à la racine du dépôt.

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CreateMediaServicesClient)]

### <a name="create-a-live-event"></a>Créer un événement en temps réel

Cette section montre comment créer un type de **transmission directe** d’événement en direct (LiveEventEncodingType défini sur Aucun). Pour plus d’informations sur les autres types d’événements en direct disponibles, consultez [Types d’événements en direct](live-event-outputs-concept.md#live-event-types). En plus de Pass-through, vous pouvez utiliser un événement en direct de transcodage en temps réel pour un encodage cloud à débit adaptatif 720P ou 1080P. 
 
Voici quelques éléments que vous voudrez probablement spécifier lors de la création de l’événement en temps réel :

* Protocole d’ingestion de l’événement en direct (les protocoles RTMP(S) et Smooth Streaming sont pris en charge).<br/>Vous ne pouvez pas changer l’option de protocole pendant l’exécution de l’événement en direct ou des sorties en direct qui lui sont associées. Si vous avez besoin d’autres protocoles, créez des événements en direct distincts pour chaque protocole de streaming.  
* Restictions IP sur l’ingestion et la préversion. Vous pouvez définir les adresses IP autorisées à recevoir du contenu vidéo sur cet événement en direct. Les adresses IP autorisées peuvent être définies sous forme d’adresse IP unique (par exemple, « 10.0.0.1 »), de plage d’adresses IP constituée d’une adresse IP et d’un masque de sous-réseau CIDR (par exemple, « 10.0.0.1/22 ») ou de plage d’adresses IP constituée d’une adresse IP et d’un masque de sous-réseau au format décimal séparé par des points (par exemple, « 10.0.0.1(255.255.252.0) »).<br/>Si aucune adresse IP n’est spécifiée et qu’il n’existe pas de définition de règle, alors aucune adresse IP ne sera autorisée. Pour autoriser toutes les adresses IP, créez une règle et définissez la valeur 0.0.0.0/0.<br/>Les adresses IP doivent utiliser un des formats suivants : adresses IPv4 à quatre chiffres ou plage d’adresses CIDR.
* Lors de la création de l’événement, vous pouvez spécifier qu’il démarre automatiquement. <br/>Lorsque le démarrage automatique est défini sur true, l’événement en direct démarre après sa création. La facturation commence donc dès le démarrage de l’événement en direct. Vous devez appeler explicitement la commande Stop sur la ressource de l’événement en direct pour arrêter toute facturation supplémentaire. Pour plus d’informations, consultez [États et facturation des événements en direct](live-event-states-billing-concept.md).
Il existe également des modes veille qui permettent de démarrer l’événement en direct dans un état « alloué » à moindre coût qui accélère le passage à l’état « En cours d’exécution ». C’est utile dans les cas où, par exemple, les hotpools doivent remettre les canaux rapidement aux streamers.
* Pour qu’une URL d’ingestion soit prévisible et plus facile à gérer dans un encodeur live matériel, affectez la valeur true à la propriété « useStaticHostname ». Pour plus d’informations, consultez [URL de réception des événements en direct](live-event-outputs-concept.md#live-event-ingest-urls).

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CreateLiveEvent)]

### <a name="get-ingest-urls"></a>Obtenir les URL de réception

Après la création de l’événement en direct, vous pouvez obtenir des URL d’ingestion que vous fournirez à l’encodeur live. L’encodeur utilise ces URL pour entrer un flux dynamique.

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#GetIngestURL)]

### <a name="get-the-preview-url"></a>Obtenir l’URL d’aperçu

Utilisez le point de terminaison d’aperçu et vérifiez que l’entrée de l’encodeur est bien reçue.

> [!IMPORTANT]
> Veillez à ce que la vidéo transite par l’URL d’aperçu avant de poursuivre.

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#GetPreviewURLs)]

### <a name="create-and-manage-live-events-and-live-outputs"></a>Créer et gérer des événements en direct et des sorties en direct

Une fois que le flux transite dans l’événement en direct, vous pouvez commencer l’événement de streaming en créant un actif multimédia, une sortie en direct et un localisateur de streaming. Le flux est alors archivé et mis à la disposition des observateurs via le point de terminaison de diffusion en continu.

Lorsque vous apprenez ces concepts, le mieux est de penser à l’objet « actif multimédia » comme si c’était la cassette que vous insériez dans un magnétoscope à l’époque. La « sortie en direct » est le magnétoscope. « L’événement en direct » est simplement le signal vidéo arrivant à l’arrière de l’appareil.

Vous créez d’abord le signal en créant l’événement en direct.  Le signal n’est pas transmis tant que vous n’avez pas démarré cet événement en direct et que vous n’avez pas connecté votre encodeur à l’entrée.

La cassette peut être créée à tout moment. Il s’agit juste d’un « actif multimédia » vide que vous allez transmettre à l’objet de sortie en direct, le magnétoscope dans cette analogie.

Le magnétoscope peut être créé à tout moment. Cela signifie que vous pouvez créer une sortie en direct avant de démarrer le flux du signal, ou après. Si vous avez besoin d’accélérer les opérations, il est parfois utile de la créer avant de démarrer le flux du signal.

Pour arrêter le magnétoscope, vous devez appeler la fonction de suppression sur la sortie en direct. Cette opération ne supprime pas le contenu de la cassette « actif multimédia ».  L’actif multimédia est toujours conservé avec le contenu vidéo archivé tant que vous n’appelez pas la fonction de suppression explicitement sur l’actif multimédia lui-même.

La section suivante vous guidera tout au long de la création de l’actif multimédia (« cassette ») et de la sortie en direct (« magnétophone »).

#### <a name="create-an-asset"></a>Créer une ressource

Créez un actif multimédia pour la sortie en direct à utiliser. Dans l’analogie ci-dessus, il s’agit de la cassette sur laquelle nous enregistrons le signal vidéo en direct. Les visiteurs peuvent voir le contenu en direct ou à la demande à partir de cette cassette virtuelle.

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CreateAsset)]

#### <a name="create-a-live-output"></a>Créer une sortie en direct

Les sorties en direct démarrent dès leur création et s’arrêtent à leur suppression. Il s’agit du « magnétoscope » pour notre événement. Quand vous supprimez la sortie en direct, vous ne supprimez pas l’actif multimédia sous-jacent ou le contenu de celui-ci. Cela revient à éjecter la cassette. L’actif multimédia contenant l’enregistrement dure aussi longtemps que vous le souhaitez, et quand il est éjecté (c’est-à-dire, quand la sortie en direct est supprimée), il est immédiatement disponible pour le visionnage à la demande. 

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CreateLiveOutput)]

#### <a name="create-a-streaming-locator"></a>Créer un localisateur de streaming

> [!NOTE]
> Après la création de votre compte Media Services, un point de terminaison de streaming **par défaut** est ajouté à votre compte à l’état **Arrêté**. Pour démarrer le streaming de votre contenu et tirer parti de l’[empaquetage dynamique](encode-dynamic-packaging-concept.md) et du chiffrement dynamique, le point de terminaison de streaming à partir duquel vous souhaitez diffuser du contenu doit se trouver à l’état **En cours d’exécution**.

Quand vous publiez l’actif multimédia à l’aide d’un localisateur de streaming, l’événement en direct (jusqu’à la longueur de la fenêtre DVR) reste visible jusqu’à l’expiration ou la suppression du localisateur de streaming, en fonction de ce qui se produit en premier. C’est comme cela que vous pouvez mettre l’enregistrement sur « cassette virtuelle » à la disposition de votre public pour le regarder en direct et à la demande. La même URL peut être utilisée pour regarder l’événement en direct, la fenêtre DVR ou l’actif multimédia à la demande quand l’enregistrement est terminé (quand la sortie en direct est supprimée).

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CreateStreamingLocator)]

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
* Arrêtez l’événement en direct. Une fois l’événement en direct arrêté, aucun frais n’est encouru. Lorsque vous devez le redémarrer, il possède la même URL de réception. Vous n’avez donc pas besoin de reconfigurer votre encodeur.
* Vous pouvez arrêter votre point de terminaison de diffusion en continu, sauf si vous souhaitez continuer à fournir l’archive de votre événement en direct en tant que flux à la demande. Si l’événement en direct est à l’état Arrêté, aucun frais n’est encouru.

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CleanupLiveEventAndOutput)]

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CleanupLocatorAssetAndStreamingEndpoint)]

## <a name="watch-the-event"></a>Regarder l’événement

Pour regarder l’événement, copiez l’URL de streaming obtenue à l’exécution du code décrit dans Créer un localisateur de streaming. Vous pouvez utiliser le lecteur multimédia de votre choix. [Lecteur multimédia Azure](https://amp.azure.net/libs/amp/latest/docs/index.html) vous permet de tester votre flux à l’adresse https://ampdemo.azureedge.net.

Une fois arrêté, l’événement en direct est automatiquement converti en contenu à la demande. Même après l’arrêt et la suppression de l’événement, les utilisateurs pourront lire votre contenu archivé en tant que vidéo à la demande tant que vous n’aurez pas supprimé l’élément multimédia. Vous ne pouvez pas supprimer d’élément multimédia s’il est utilisé par un événement. Vous devez d’abord supprimer l’événement.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous n’avez plus besoin des ressources de votre groupe de ressources, notamment les comptes de stockage et Media Services que vous avez créés pour ce didacticiel, supprimez le groupe de ressources créé précédemment.

Exécutez la commande CLI suivante :

```azurecli-interactive
az group delete --name amsResourceGroup
```

> [!IMPORTANT]
> Laisser l’événement en direct s’exécuter entraîne des frais. Sachez que si le projet/programme plante ou est fermé pour une quelconque raison, l’événement peut continuer de s’exécuter et ainsi entraîner des frais.

## <a name="ask-questions-give-feedback-get-updates"></a>Poser des questions, envoyer des commentaires, obtenir des mises à jour

Découvrez l’article [Communauté Azure Media Services](media-services-community.md) pour découvrir les différentes façons dont vous pouvez poser des questions, faire des commentaires et obtenir des mises à jour sur Media Services.

## <a name="next-steps"></a>Étapes suivantes

[Diffuser des fichiers](stream-files-tutorial-with-api.md)
 
