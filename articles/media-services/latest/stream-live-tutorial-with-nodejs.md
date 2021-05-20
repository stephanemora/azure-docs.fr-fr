---
title: Streaming en direct avec Media Services en utilisant Node.js et TypeScript
titleSuffix: Azure Media Services
description: Découvrez comment streamer des événements en direct en utilisant Node.js, TypeScript et OBS Studio.
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
ms.custom: mvc, devx-track-nodejs
ms.date: 04/15/2021
ms.author: inhenkel
ms.openlocfilehash: 5b7c080e532a7a8cb220a501fb7239300b3f2d3e
ms.sourcegitcommit: 5da0bf89a039290326033f2aff26249bcac1fe17
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2021
ms.locfileid: "109712871"
---
# <a name="tutorial-stream-live-with-media-services-by-using-nodejs-and-typescript"></a>Tutoriel : Streaming en direct avec Media Services en utilisant Node.js et TypeScript

Dans Azure Media Services, les [événements en direct](/rest/api/media/liveevents) sont responsables du traitement de contenu de streaming en direct. Un événement en direct fournit un point de terminaison d’entrée (URL d’ingestion) que vous fournissez ensuite à un encodeur live. Il reçoit des flux d’entrée d’un encodeur live et les rend disponibles pour le streaming par le biais d’un ou de plusieurs [points de terminaison de streaming](/rest/api/media/streamingendpoints). Les événements en direct fournissent également un point de terminaison d’aperçu (URL d’aperçu) que vous utilisez pour prévisualiser et valider votre flux avant de poursuivre le traitement ou la distribution. 

Ce tutoriel montre comment utiliser Node.js et TypeScript pour créer un type *Pass-through* d’un événement en direct et pour diffuser un stream en direct sur celui-ci en utilisant [OBS Studio](https://obsproject.com/download).

Ce didacticiel présente les procédures suivantes :

> [!div class="checklist"]
> * Téléchargez l’exemple de code.
> * Examiner le code qui configure et effectue le streaming en direct.
> * Regardez l’événement avec le [Lecteur multimédia Azure](https://amp.azure.net/libs/amp/latest/docs/index.html) sur le [site de démonstration du Lecteur multimédia](https://ampdemo.azureedge.net).
> * Supprimer des ressources.


[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> Bien que le tutoriel utilise des exemples Node.js, les étapes générales sont les mêmes pour l’[API REST](/rest/api/media/liveevents), l’[interface CLI](/cli/azure/ams/live-event) ou d’autres [SDK](media-services-apis-overview.md#sdks) pris en charge. 

## <a name="prerequisites"></a>Prérequis

Pour ce tutoriel, vous devez disposer des éléments suivants :

- Installez [Node.js](https://nodejs.org/en/download/).
- Installez [TypeScript](https://www.typescriptlang.org/).
- [Créer un compte Media Services](./create-account-howto.md). Mémorisez les valeurs que vous utilisez pour le nom du groupe de ressources et le nom du compte Media Services.
- Suivez les étapes décrites dans [Accéder à l’API Azure Media Services avec Azure CLI](./access-api-howto.md) et enregistrez les informations d’identification. Vous en aurez besoin pour accéder à l’API et configurer votre fichier de variables d’environnement.
- Parcourez l’article [Configurer et se connecter à Node.js](./configure-connect-nodejs-howto.md) pour comprendre comment utiliser le SDK client Node.js.
- Installez Visual Studio Code ou Visual Studio.
- [Configurez votre environnement Visual Studio Code](https://code.visualstudio.com/Docs/languages/typescript) pour prendre en charge le langage TypeScript.

Vous avez besoin de ces éléments supplémentaires pour les logiciels de streaming en direct :

- Une caméra ou appareil (tel qu’un ordinateur portable) utilisé pour diffuser un événement.
- Un encodeur logiciel local qui encode votre flux de caméra et l’envoie au service de streaming en direct Media Services via le protocole RTMP (Real-Time Messaging Protocol). Pour plus d’informations, consultez [Encodeurs live locaux recommandés](encode-recommended-on-premises-live-encoders.md). La diffusion doit se faire au format RTMP ou Smooth Streaming.

  Cet exemple part du principe que vous allez utiliser Open Broadcaster Software (OBS) Studio pour diffuser le RTMP au point de terminaison d’ingestion. [Installez OBS Studio](https://obsproject.com/download). 

  Utilisez les paramètres d’encodage suivants dans OBS Studio :

  - Encodeur : NVIDIA NVENC (si disponible) ou x264
  - Contrôle de la fréquence : CBR
  - Débit binaire : 2 500 Kbits/s (ou une valeur raisonnable pour votre ordinateur)
  - Intervalle d’image clé : 2 s ou 1 s pour une latence faible  
  - Présélection : Qualité pour latence faible ou Performance (NVENC) ou « très rapide » en utilisant x264
  - Profil : élevé
  - GPU : 0 (Auto)
  - Nombre maximal d’images de type B : 2

> [!TIP]
> Consultez [Streaming en direct avec Media Services v3](stream-live-streaming-concept.md) avant de commencer.

## <a name="download-and-configure-the-sample"></a>Télécharger et configurer l’exemple

Clonez le dépôt GitHub qui contient l’exemple Node.js de streaming en direct sur votre machine en utilisant la commande suivante :  

```bash
git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
```

L’exemple de streaming en direct se trouve dans le dossier [Live](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/Live).

Dans le dossier [AMSv3Samples](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples), copiez le fichier nommé *sample.env* vers un nouveau fichier appelé *.env* pour stocker les valeurs des variables d’environnement que vous avez rassemblées dans l’article [Accéder à l’API Azure Media Services avec Azure CLI](./access-api-howto.md).
Vérifiez que le nom du fichier contient le point (.) devant « .env » pour qu’il fonctionne correctement avec l’exemple de code.

Le [fichier .env](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/main/AMSv3Samples/sample.env) contient la clé et le secret de votre application Azure Active Directory (Azure AD). Il contient également le nom de compte et les informations d’abonnement nécessaires pour authentifier l’accès du SDK à votre compte Media Services. Le fichier *.gitignore* est déjà configuré pour empêcher la publication de ce fichier dans votre dépôt dupliqué. N’autorisez aucune fuite de ces informations d’identification, car il s’agit de secrets importants pour votre compte.

> [!IMPORTANT]
> Cet exemple utilise un suffixe unique pour chaque ressource. Si vous annulez le débogage ou vous fermez l’application sans l’avoir effectué, vous obtiendrez plusieurs événements en direct sur votre compte. 
>
> Veillez à arrêter les événements en direct en cours d’exécution. Sinon, *vous serez facturé* ! Exécutez le programme jusqu’à la fin pour nettoyer automatiquement les ressources. Si le programme s’arrête, ou si vous arrêtez par inadvertance le débogueur et que vous mettez fin l’exécution du programme, vous devez bien vérifier le portail pour être sûr que vous n’avez pas laissé d’événements en direct dans l’état En cours d’exécution ou en attente, ce qui entraînerait des coûts facturés non souhaités.

## <a name="examine-the-typescript-code-for-live-streaming"></a>Examiner le code TypeScript pour le streaming en direct

Cette section examine les fonctions définies dans le fichier [index.ts](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/main/AMSv3Samples/Live/index.ts) du projet *Live*.

L’exemple crée un suffixe unique pour chaque ressource afin d’éviter tout conflit de noms si vous exécutez l’exemple plusieurs fois sans le supprimer.

### <a name="start-using-the-media-services-sdk-for-nodejs-with-typescript"></a>Commencer à utiliser le SDK Media Services pour Node.js avec TypeScript

Pour commencer à utiliser des API Media Services avec Node.js, vous devez d’abord ajouter le module du SDK [@azure/arm-mediaservices](https://www.npmjs.com/package/@azure/arm-mediaservices) en utilisant le gestionnaire de package npm :

```bash
npm install @azure/arm-mediaservices
```

Dans le fichier *package.js*, cela est déjà configuré pour vous. Vous devez simplement exécuter `npm install` pour charger les modules et les dépendances :

1. Ouvrez une invite de commandes, puis accédez au répertoire de l’exemple.
1. Accédez au dossier *AMSv3Samples* :

    ```bash
    cd AMSv3Samples
    ```

1. Installez les packages utilisés dans le fichier *packages.json* :

    ```bash
    npm install 
    ```

1. Ouvrez Visual Studio Code à partir du dossier *AMSv3Samples*. (C’est nécessaire pour démarrer à partir du dossier où se trouvent le dossier *.vscode* et les fichiers *tsconfig.js*.)

    ```bash
    cd ..
    code .
    ```

Ouvrez le dossier pour *Live*, puis ouvrez le fichier *index.ts* dans l’éditeur Visual Studio Code.

Pendant que vous êtes dans le fichier *index.ts*, appuyez sur la touche F5 pour ouvrir le débogueur.

### <a name="create-the-media-services-client"></a>Créer le client Media Services

L’extrait de code suivant montre comment créer le client Media Services dans Node.js.

Dans ce code, vous définissez la propriété `longRunningOperationRetryTimeout` de `AzureMediaServicesOptions` sur 2 secondes à la place de la valeur par défaut de 30 secondes. Cette modification réduit le temps nécessaire pour interroger l’état d’une opération de longue durée sur le point de terminaison Azure Resource Manager. Elle réduit le temps nécessaire pour effectuer des opérations majeures, telles que la création d’événements en direct, le démarrage et l’arrêt, qui sont toutes des appels asynchrones. Nous recommandons une valeur de 2 secondes pour la plupart des scénarios.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateMediaServicesClient)]

### <a name="create-a-live-event"></a>Créer un événement en temps réel

Cette section montre comment créer un type de *Pass-through* d’événement en direct (`LiveEventEncodingType` défini sur `None`). Pour plus d’informations sur les types disponibles, consultez [Types d’événements en direct](live-event-outputs-concept.md#live-event-types). En plus de Pass-through, vous pouvez utiliser un événement d’encodage live pour un encodage cloud à débit adaptatif 720p ou 1080p.
 
Vous souhaiterez peut-être spécifier les éléments suivants lors de la création de l’événement en direct :

* **Protocole d’ingestion pour l’événement en direct**. Les protocoles RTMP, RTMPS et Smooth Streaming sont pris en charge. Vous ne pouvez pas changer l’option de protocole pendant l’exécution de l’événement en direct ou des sorties en direct qui lui sont associées. Si vous avez besoin d’autres protocoles, créez des événements en direct distincts pour chaque protocole de streaming.  
* **Restrictions IP sur l’ingestion et l’aperçu**. Vous pouvez définir les adresses IP autorisées à recevoir du contenu vidéo sur cet événement en direct. Les adresses IP autorisées peuvent être spécifiées sous l’une des formes suivantes :

  * Une adresse IP unique (par exemple, `10.0.0.1`)
  * Plage d’adresses IP qui utilise une adresse IP et un masque de sous-réseau de routage CIDR (Classless InterDomain Routing) (par exemple, `10.0.0.1/22`)
  * Plage d’adresses IP utilisant une adresse IP et un masque de sous-réseau décimal séparé par des points (par exemple, `10.0.0.1(255.255.252.0)`)

  Si aucune adresse IP n’est spécifiée et qu’il n’existe pas de définition de règle, alors aucune adresse IP ne sera autorisée. Pour autoriser toutes les adresses IP, créez une règle et définissez la valeur `0.0.0.0/0`. Les adresses IP doivent utiliser un des formats suivants : adresses IPv4 à quatre chiffres ou une plage d’adresses CIDR.
* **Démarrage automatique d’un événement quand vous le créez**. Quand le démarrage automatique est défini sur `true`, l’événement en direct démarre après sa création. La facturation commence donc dès le démarrage de l’événement en direct. Vous devez appeler explicitement la commande `Stop` sur la ressource de l’événement en direct pour arrêter toute facturation supplémentaire. Pour plus d’informations, consultez [États et facturation des événements en direct](live-event-states-billing-concept.md).

  Des modes veille permettent de démarrer l’événement en direct dans un état « alloué » à moindre coût qui accélère le passage à l’état En cours d’exécution. C’est utile dans certains, par exemple quand des pools très sollicités doivent distribuer rapidement des canaux aux diffuseurs.
* **Un nom d’hôte statique et un GUID unique**. Pour qu’une URL d’ingestion soit prévisible et plus facile à gérer dans un encodeur live matériel, affectez la valeur `true` à la propriété `useStaticHostname`. Pour `accessToken`, utilisez un GUID personnalisé unique. Pour plus d’informations, consultez [URL d’ingestion des événements en direct](live-event-outputs-concept.md#live-event-ingest-urls).

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateLiveEvent)]

### <a name="create-an-asset-to-record-and-archive-the-live-event"></a>Créer un actif multimédia pour enregistrer et archiver l’événement en direct

Dans le bloc de code suivant, vous allez créer un actif multimédia vide à utiliser comme « bande » pour enregistrer votre archive d’événements en direct.

Quand vous apprenez ces concepts, il est utile de penser à l’objet d’actif multimédia comme si c’était la cassette que vous insériez dans un magnétoscope à l’époque. La sortie en direct est le magnétoscope. L’événement en direct est simplement le signal vidéo arrivant à l’arrière de l’appareil.

Gardez à l’esprit que l’actif multimédia, ou « bande », peut être créé à tout moment. Vous allez transmettre l’actif multimédia vide à l’objet de sortie en direct, le « magnétoscope » dans cette analogie.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateAsset)]

### <a name="create-the-live-output"></a>Créer la sortie en direct

Dans cette section, vous créez une sortie en direct qui utilise le nom de l’actif multimédia comme entrée pour indiquer l’emplacement où enregistrer l’événement en direct. En outre, vous configurez la fenêtre de décalage temporel (DVR) à utiliser dans l’enregistrement.

L’exemple de code montre comment configurer une fenêtre de décalage temporel d’une heure. Cette fenêtre permet aux clients de lire toute donnée dans la dernière heure de l’événement. De plus, seule la dernière heure de l’événement en direct va rester dans l’archive. Vous pouvez étendre la durée de cette fenêtre à 25 heures si nécessaire.  Notez également que vous pouvez contrôler le nom du manifeste de sortie utilisé par les manifestes HLS (HTTP Live Streaming) et DASH (Dynamic Adaptive Streaming over HTTP) dans vos chemins d’URL lors de leur publication.

La sortie en direct, ou le « magnétophone », dans notre analogie, peut également être créée à tout moment. Vous pouvez créer une sortie en direct avant de démarrer le flux du signal, ou après. Si vous avez besoin d’accélérer les opérations, il est souvent utile de créer la sortie avant de démarrer le flux du signal.

Les sorties en direct démarrent quand elles sont créées et s’arrêtent quand elles sont supprimées.  Quand vous supprimez la sortie en direct, vous ne supprimez pas l’actif multimédia sous-jacent ou le contenu de celui-ci. Cela revient à éjecter la « cassette ». L’actif multimédia contenant l’enregistrement dure aussi longtemps que vous le souhaitez. Quand il est éjecté (c’est-à-dire, quand la sortie en direct est supprimée), il est immédiatement disponible pour un visionnage à la demande.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateLiveOutput)]


### <a name="get-ingest-urls"></a>Obtenir les URL de réception

Après la création de l’événement en direct, vous pouvez obtenir des URL d’ingestion que vous fournirez à l’encodeur live. L’encodeur utilise ces URL pour entrer un stream en direct en utilisant le protocole RTMP.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#GetIngestURL)]

### <a name="get-the-preview-url"></a>Obtenir l’URL d’aperçu

Utilisez `previewEndpoint` pour obtenir un aperçu et vérifier que l’entrée de l’encodeur est bien reçue.

> [!IMPORTANT]
> Avant de poursuivre, veillez à ce que la vidéo transite par l’URL d’aperçu.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#GetPreviewURL)]

### <a name="create-and-manage-live-events-and-live-outputs"></a>Créer et gérer des événements en direct et des sorties en direct

Après que le stream transite dans l’événement en direct, vous pouvez commencer l’événement de streaming en publiant un localisateur de streaming que vos lecteurs clients vont utiliser. Les visiteurs pourront ainsi l’avoir à disposition via le point de terminaison de streaming.

Vous créez d’abord le signal en créant l’événement en direct. Le signal n’est pas transmis tant que vous n’avez pas démarré cet événement en direct et que vous n’avez pas connecté votre encodeur à l’entrée.

Pour arrêter le « magnétoscope », vous appelez `delete` sur `LiveOutput`. Cette action ne supprime pas le *contenu* de votre archive sur la « bande » (actif multimédia). Elle supprime uniquement le « magnétoscope » et arrête l’archivage. L’actif multimédia est toujours conservé avec le contenu vidéo archivé tant que vous n’appelez pas `delete` explicitement sur l’actif multimédia lui-même. Dès que vous supprimez `LiveOutput`, le contenu enregistré de l’actif multimédia reste disponible pour la lecture via les URL de localisateur de streaming publiées. 

Si vous voulez supprimer la possibilité pour un client de lire le contenu archivé, vous devez d’abord supprimer tous les localisateurs de l’actif multimédia. Vous videz aussi le cache CDN (réseau de distribution de contenu) sur le chemin de l’URL si vous utilisez un CDN pour la distribution. Sinon, le contenu reste dans le cache du CDN pendant un temps déterminé par la valeur de la durée de vie standard sur le CDN (qui peut atteindre jusqu’à 72 heures).

#### <a name="create-a-streaming-locator-to-publish-hls-and-dash-manifests"></a>Créer un localisateur de streaming pour publier des manifestes HLS et DASH

> [!NOTE]
> Une fois votre compte Media Services créé, un point de terminaison de streaming par défaut est ajouté à votre compte dans l’état Arrêté. Pour démarrer le streaming de votre contenu et tirer parti de l’[empaquetage dynamique](encode-dynamic-packaging-concept.md) et du chiffrement dynamique, le point de terminaison de streaming à partir duquel vous souhaitez diffuser du contenu doit se trouver à l’état En cours d’exécution.

Quand vous publiez l’actif multimédia en utilisant un localisateur de streaming, l’événement en direct (jusqu’à la longueur de la fenêtre DVR) reste visible jusqu’à l’expiration ou la suppression du localisateur de streaming, en fonction de ce qui se produit en premier. C’est comme cela que vous pouvez mettre l’enregistrement sur « cassette » virtuelle à la disposition de votre public pour le regarder en direct et à la demande. La même URL peut être utilisée pour regarder l’événement en direct, la fenêtre DVR ou l’actif multimédia à la demande quand l’enregistrement est terminé (quand la sortie en direct est supprimée).

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateStreamingLocator)]

#### <a name="build-the-paths-to-the-hls-and-dash-manifests"></a>Créer les chemins aux manifestes HLS et DASH

La méthode `BuildManifestPaths` de l’exemple montre comment créer de façon déterministe les chemins de streaming à utiliser pour la distribution HLS ou DASH à différents clients et infrastructures de lecteur.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#BuildManifestPaths)]

## <a name="watch-the-event"></a>Regarder l’événement

Pour regarder l’événement, copiez l’URL de streaming que vous avez obtenue quand vous avez exécuté le code qui permet de créer un localisateur de streaming. Vous pouvez utiliser le lecteur multimédia de votre choix. Le [Lecteur multimédia Azure](https://amp.azure.net/libs/amp/latest/docs/index.html) est disponible pour tester votre flux sur le [site de démonstration du Lecteur multimédia](https://ampdemo.azureedge.net).

Une fois arrêté, un événement en direct est automatiquement converti en contenu à la demande. Même après l’arrêt et la suppression de l’événement, les utilisateurs pourront lire votre contenu archivé en tant que vidéo à la demande tant que vous n’aurez pas supprimé l’élément multimédia. Vous ne pouvez pas supprimer un actif multimédia si un événement est en train de l’utiliser. Vous devez d’abord supprimer l’événement.

## <a name="clean-up-resources-in-your-media-services-account"></a>Supprimer les ressources de votre compte Media Services

Si vous exécutez l’application jusqu’au bout, elle va nettoyer automatiquement toutes les ressources utilisées dans la fonction `cleanUpResources`. Vérifiez que l’application ou le débogueur s’exécute jusqu’au bout ; sinon, vous risqueriez de perdre des ressources et de vous retrouver avec des événements en direct dans votre compte. Vérifiez bien dans le portail Azure que toutes les ressources sont nettoyées dans votre compte Media Services. 

Dans l’exemple de code, reportez-vous à la méthode `cleanUpResources` pour plus d’informations.

> [!IMPORTANT]
> Le fait de laisser l’événement en direct s’exécuter entraîne des frais. Sachez que si le projet ou le programme cesse de répondre ou est fermé pour une raison quelconque, l’événement en direct peut continuer de s’exécuter et ainsi entraîner des frais.

## <a name="ask-questions-give-feedback-get-updates"></a>Poser des questions, envoyer des commentaires, obtenir des mises à jour

Découvrez l’article [Communauté Azure Media Services](media-services-community.md) pour découvrir les différentes façons dont vous pouvez poser des questions, faire des commentaires et obtenir des mises à jour sur Media Services.

## <a name="more-developer-documentation-for-nodejs-on-azure"></a>Documentation supplémentaire pour les développeurs pour Node.js sur Azure

- [Azure pour les développeurs JavaScript et Node.js](/azure/developer/javascript/)
- [Code source de Media Services dans le dépôt GitHub @azure/azure-sdk-for-js](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)
- [Documentation sur les packages Azure pour les développeurs Node.js](/javascript/api/overview/azure/)


## <a name="next-steps"></a>Étapes suivantes

[Diffuser des fichiers](stream-files-tutorial-with-api.md)