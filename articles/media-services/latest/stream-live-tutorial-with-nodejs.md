---
title: Streaming en direct avec Media Services v3 Node.js
titleSuffix: Azure Media Services
description: Découvrez le streaming en direct en utilisant Node.js.
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
ms.openlocfilehash: 749d2fc845f036a2802c80c161b3fc8c171c2555
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730193"
---
# <a name="tutorial-stream-live-with-media-services-using-nodejs-and-typescript"></a>Tutoriel : Streaming en direct avec Media Services en utilisant Node.js et TypeScript

> [!NOTE]
> Bien que le tutoriel utilise des exemples Node.js, les étapes générales sont les mêmes pour l’[API REST](/rest/api/media/liveevents), l’[interface CLI](/cli/azure/ams/live-event) ou d’autres [SDK](media-services-apis-overview.md#sdks) pris en charge. 

Dans Azure Media Services, les [événements en direct](/rest/api/media/liveevents) sont responsables du traitement de contenu de streaming en direct. Un événement en direct fournit un point de terminaison d’entrée (URL de réception) que vous fournissez ensuite à un encodeur live. Il reçoit des flux d’entrée en temps réel d’un encodeur live et le rend disponible pour le streaming par le biais d’un ou de plusieurs [points de terminaison de streaming](/rest/api/media/streamingendpoints). Les événements en direct fournissent également un point de terminaison d’aperçu (URL d’aperçu) que vous utilisez pour prévisualiser et valider votre flux avant de poursuivre le traitement ou la distribution. Ce tutoriel montre comment utiliser Node.js pour créer un type **pass-through** d’un événement en direct et pour diffuser un flux en direct vers celui-ci en utilisant [OBS Studio](https://obsproject.com/download).

Ce didacticiel explique les procédures suivantes :

> [!div class="checklist"]
> * Télécharger l’exemple de code décrit dans la rubrique.
> * Examiner le code qui configure et effectue le streaming en direct.
> * Surveiller l’événement avec le [Lecteur multimédia Azure](https://amp.azure.net/libs/amp/latest/docs/index.html) à l’adresse [https://ampdemo.azureedge.net](https://ampdemo.azureedge.net).
> * Supprimer des ressources.


[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

Les éléments suivants sont requis pour suivre le didacticiel :

- Installez [Node.js](https://nodejs.org/en/download/)
- Installez [TypeScript](https://www.typescriptlang.org/)
- [Créer un compte Media Services](./create-account-howto.md).<br/>Veillez à mémoriser les valeurs que vous avez utilisées pour le nom du groupe de ressources et le nom du compte Media Services.
- Suivez les étapes décrites dans [Accéder à l’API Azure Media Services avec Azure CLI](./access-api-howto.md) et enregistrez les informations d’identification. Vous devrez les utiliser pour accéder à l’API et configurer votre fichier de variables d’environnement.
- Parcourez la procédure [Configurer et se connecter à Node.js](./configure-connect-nodejs-howto.md) pour comprendre comment utiliser le SDK client Node.js.
- Installez Visual Studio Code ou Visual Studio.
- [Configurez votre environnement Visual Studio Code](https://code.visualstudio.com/Docs/languages/typescript) pour prendre en charge le langage TypeScript.

## <a name="additional-settings-for-live-streaming-software"></a>Paramètres supplémentaires pour le logiciel de streaming en direct

- Une caméra ou appareil (tel qu’un ordinateur portable) utilisé pour diffuser un événement.
- Un encodeur logiciel local qui encode votre flux de caméra et l’envoie au service de streaming en direct Media Services avec le protocole RTMP ; consultez les [encodeurs live locaux recommandés](encode-recommended-on-premises-live-encoders.md). La diffusion doit se faire au format **RTMP** ou **Smooth Streaming**.  
- Pour cet exemple, nous vous recommandons de commencer avec un encodeur logiciel comme le logiciel gratuit [Open Broadcast Software OBS Studio](https://obsproject.com/download).

Cet exemple part du principe que vous allez utiliser OBS Studio pour diffuser le RTMP au point de terminaison d’ingestion. Tout d’abord, installez OBS Studio.
Utilisez les paramètres d’encodage suivants dans OBS Studio :

- Encodeur : NVIDIA NVENC (si disponible) ou x264
- Contrôle de la fréquence : CBR
- Débit binaire : 2 500 Kbits/s (ou une valeur raisonnable pour votre ordinateur portable)
- Intervalle d’image clé : 2 s ou 1 s pour une latence faible  
- Présélection : Qualité pour latence faible ou Performance (NVENC) ou « très rapide » en utilisant x264
- Profil : élevé
- GPU : 0 (Auto)
- Nombre maximal d’images de type B : 2

> [!TIP]
> Veillez à consulter [Diffusion en continu avec Media Services v3](stream-live-streaming-concept.md) avant de commencer.

## <a name="download-and-configure-the-sample"></a>Télécharger et configurer l’exemple

Clonez le dépôt GitHub suivant, qui contient l’exemple Node.js de streaming en direct sur votre machine en utilisant la commande suivante :  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
 ```

L’exemple de diffusion en continu est situé dans le dossier [Live](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/Live).

Dans le dossier [AMSv3Samples](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples), copiez le fichier nommé « sample.env » vers un nouveau fichier appelé « .env » pour stocker les valeurs des variables d’environnement que vous avez rassemblées dans l’article [Accéder à l’API Azure Media Services avec Azure CLI](./access-api-howto.md).
Vérifiez que le fichier contient le « point » (.) devant « .env » pour qu’il fonctionne correctement avec l’exemple de code.

Le [fichier .env](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/main/AMSv3Samples/sample.env) contient la clé et le secret de votre application AAD ainsi que le nom de compte et les informations d’abonnement nécessaire pour authentifier l’accès du SDK à votre compte Media Services. Le fichier .gitignore est déjà configuré pour empêcher la publication de ce fichier dans votre dépôt dupliqué. N’autorisez aucune fuite de ces informations d’identification, car il s’agit de secrets importants pour votre compte.

> [!IMPORTANT]
> Cet exemple utilise un suffixe unique pour chaque ressource. Si vous annulez le débogage ou vous fermez l’application sans l’avoir effectué, vous obtiendrez plusieurs événements en direct sur votre compte. <br/>Veillez à arrêter les événements en direct en cours d’exécution. Sinon, vous serez **facturé** ! Exécutez le programme jusqu’à la fin pour nettoyer automatiquement les ressources. Si le programme plante, ou si vous arrêtez par inadvertance le débogueur et que vous mettez fin l’exécution du programme, vous devez bien vérifier le portail pour être sûr que vous n’avez pas laissé d’événements en direct dans les états En cours d’exécution ou En attente, ce qui entraînerait des coûts facturés non souhaités.

## <a name="examine-the-typescript-code-for-live-streaming"></a>Examiner le code TypeScript pour le streaming en direct

Cette section examine les fonctions définies dans le fichier [index.ts](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/main/AMSv3Samples/Live/index.ts) du projet *Live*.

L’exemple crée un suffixe unique pour chaque ressource afin d’éviter tout conflit de noms si vous exécutez l’exemple plusieurs fois sans le supprimer.

### <a name="start-using-media-services-sdk-for-nodejs-with-typescript"></a>Commencer à utiliser le SDK Media Services pour Node.js avec TypeScript

Pour commencer à utiliser des API Media Services avec Node.js, vous devez d’abord ajouter le module du SDK [@azure/arm-mediaservices](https://www.npmjs.com/package/@azure/arm-mediaservices) en utilisant le gestionnaire de package npm.

```bash
npm install @azure/arm-mediaservices
```

Dans le fichier package.json, c’est déjà configuré pour vous : vous devez donc simplement exécuter *npm install* pour charger les modules et les dépendances.

1. Ouvrez une **invite de commandes**, puis accédez au répertoire de l’exemple.
1. Accédez au dossier AMSv3Samples.

    ```bash
    cd AMSv3Samples
    ```

1. Installez les packages utilisés dans le fichier *packages.json*.

    ```bash
    npm install 
    ```

1. Lancez Visual Studio Code à partir du dossier *AMSv3Samples*. (C’est nécessaire pour effectuer le lancement à partir du dossier où se trouvent le dossier *.vscode* et les fichiers *tsconfig.js*.)

    ```bash
    cd ..
    code .
    ```

Ouvrez le dossier pour *Live*, puis ouvrez le fichier *index.ts* dans l’éditeur Visual Studio Code.
Tout en étant dans le fichier *index.ts*, appuyez sur F5 pour lancer le débogueur.

### <a name="create-the-media-services-client"></a>Créer le client Media Services

L’extrait de code suivant montre comment créer le client Media Services dans Node.js.
Notez que dans ce code, nous définissons d’abord la propriété **longRunningOperationRetryTimeout** d’AzureMediaServicesOptions sur 2 secondes pour réduire le temps nécessaire à l’interrogation de l’état d’une opération de longue durée sur le point de terminaison de gestion des ressources Azure.  Comme la plupart des opérations sur les événements en direct sont asynchrones et peuvent prendre un certain temps, vous devez réduire cet intervalle d’interrogation sur le SDK et changer sa valeur par défaut de 30 secondes pour accélérer le temps nécessaire à l’exécution des principales opérations, comme la création d’événements en direct, le démarrage et l’arrêt, qui sont tous des appels asynchrones. La valeur recommandée pour la plupart des scénarios de cas d’utilisation est de deux secondes.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateMediaServicesClient)]

### <a name="create-a-live-event"></a>Créer un événement en temps réel

Cette section montre comment créer un type de **transmission directe** d’événement en direct (LiveEventEncodingType défini sur Aucun). Pour plus d’informations sur les autres types d’événements en direct disponibles, consultez [Types d’événements en direct](live-event-outputs-concept.md#live-event-types). En plus de Pass-through, vous pouvez utiliser un événement en direct de transcodage en temps réel pour un encodage cloud à débit adaptatif 720P ou 1080P.
 
Voici quelques éléments que vous voudrez probablement spécifier lors de la création de l’événement en temps réel :

* Protocole d’ingestion de l’événement en direct (les protocoles RTMP(S) et Smooth Streaming sont pris en charge).<br/>Vous ne pouvez pas changer l’option de protocole pendant l’exécution de l’événement en direct ou des sorties en direct qui lui sont associées. Si vous avez besoin d’autres protocoles, créez des événements en direct distincts pour chaque protocole de streaming.  
* Restictions IP sur l’ingestion et la préversion. Vous pouvez définir les adresses IP autorisées à recevoir du contenu vidéo sur cet événement en direct. Les adresses IP autorisées peuvent être définies sous forme d’adresse IP unique (par exemple, « 10.0.0.1 »), de plage d’adresses IP constituée d’une adresse IP et d’un masque de sous-réseau CIDR (par exemple, « 10.0.0.1/22 ») ou de plage d’adresses IP constituée d’une adresse IP et d’un masque de sous-réseau au format décimal séparé par des points (par exemple, « 10.0.0.1(255.255.252.0) »).<br/>Si aucune adresse IP n’est spécifiée et qu’il n’existe pas de définition de règle, alors aucune adresse IP ne sera autorisée. Pour autoriser toutes les adresses IP, créez une règle et définissez la valeur 0.0.0.0/0.<br/>Les adresses IP doivent utiliser un des formats suivants : adresses IPv4 à quatre chiffres ou plage d’adresses CIDR.
* Lors de la création de l’événement, vous pouvez spécifier qu’il démarre automatiquement. <br/>Lorsque le démarrage automatique est défini sur true, l’événement en direct démarre après sa création. La facturation commence donc dès le démarrage de l’événement en direct. Vous devez appeler explicitement la commande Stop sur la ressource de l’événement en direct pour arrêter toute facturation supplémentaire. Pour plus d’informations, consultez [États et facturation des événements en direct](live-event-states-billing-concept.md).
Il existe également des modes veille qui permettent de démarrer l’événement en direct dans un état « alloué » à moindre coût qui accélère le passage à l’état « En cours d’exécution ». C’est utile dans certains, par exemple quand des pools très sollicités doivent distribuer rapidement des canaux aux diffuseurs.
* Pour qu’une URL d’ingestion soit prévisible et plus facile à gérer dans un encodeur en direct matériel, définissez la propriété « useStaticHostname » sur true et utilisez un GUID unique personnalisé dans le jeton « accessToken ». Pour plus d’informations, consultez [URL de réception des événements en direct](live-event-outputs-concept.md#live-event-ingest-urls).

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateLiveEvent)]

### <a name="create-an-asset-to-record-and-archive-the-live-event"></a>Créer un actif multimédia pour enregistrer et archiver l’événement en direct

Dans ce bloc de code, vous allez créer un actif multimédia vide à utiliser comme « bande » pour enregistrer votre archive d’événements en direct.
Lorsque vous apprenez ces concepts, le mieux est de penser à l’objet « actif multimédia » comme si c’était la cassette que vous insériez dans un magnétoscope à l’époque. La « sortie en direct » est le magnétoscope. « L’événement en direct » est simplement le signal vidéo arrivant à l’arrière de l’appareil.

Gardez à l’esprit que l’actif multimédia, ou « bande », peut être créé à tout moment. Il s’agit juste d’un « actif multimédia » vide que vous allez transmettre à l’objet de sortie en direct, le magnétoscope dans cette analogie.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateAsset)]

### <a name="create-the-live-output"></a>Créer la sortie en direct

Dans cette section, nous créons une sortie en direct qui utilise le nom de l’actif multimédia comme entrée pour indiquer l’emplacement où enregistrer l’événement en direct. En outre, nous avons configuré la fenêtre de décalage temporel (DVR) à utiliser dans l’enregistrement.
L’exemple de code montre comment configurer une fenêtre de décalage temporel d’une heure. Ceci permet aux clients de lire n’importe où dans la dernière heure de l’événement.  De plus, seule la dernière heure de l’événement en direct va rester dans l’archive. Vous pouvez étendre cette durée jusqu’à 25 heures si nécessaire.  Notez aussi que vous pouvez contrôler le nom du manifeste de sortie utilisé par les manifestes HLS et DASH dans vos chemins d’URL lors de la publication.

La sortie en direct, ou le « magnétophone », dans notre analogie, peut également être créée à tout moment. Cela signifie que vous pouvez créer une sortie en direct avant de démarrer le flux du signal, ou après. Si vous avez besoin d’accélérer les opérations, il est souvent utile de la créer avant de démarrer le flux du signal.

Les sorties en direct démarrent dès leur création et s’arrêtent à leur suppression.  Quand vous supprimez la sortie en direct, vous ne supprimez pas l’actif multimédia sous-jacent ou le contenu de celui-ci. Cela revient à éjecter la cassette. L’actif multimédia contenant l’enregistrement dure aussi longtemps que vous le souhaitez, et quand il est éjecté (c’est-à-dire, quand la sortie en direct est supprimée), il est immédiatement disponible pour le visionnage à la demande.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateLiveOutput)]


### <a name="get-ingest-urls"></a>Obtenir les URL de réception

Après la création de l’événement en direct, vous pouvez obtenir des URL d’ingestion que vous fournirez à l’encodeur live. L’encodeur utilise ces URL pour entrer un stream en direct en utilisant le protocole RTMP.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#GetIngestURL)]

### <a name="get-the-preview-url"></a>Obtenir l’URL d’aperçu

Utilisez le point de terminaison d’aperçu et vérifiez que l’entrée de l’encodeur est bien reçue.

> [!IMPORTANT]
> Veillez à ce que la vidéo transite par l’URL d’aperçu avant de poursuivre.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#GetPreviewURL)]

### <a name="create-and-manage-live-events-and-live-outputs"></a>Créer et gérer des événements en direct et des sorties en direct

Une fois que le stream transite dans l’événement en direct, vous pouvez commencer l’événement de streaming en publiant un localisateur de streaming que vos lecteurs clients vont utiliser. Les visiteurs pourront ainsi l’avoir à disposition via le point de terminaison de streaming.

Vous créez d’abord le signal en créant l’événement en direct.  Le signal n’est pas transmis tant que vous n’avez pas démarré cet événement en direct et que vous n’avez pas connecté votre encodeur à l’entrée.

Pour arrêter le « magnétoscope », vous appelez la fonction de suppression sur la sortie en direct. Ceci ne supprime pas réellement le **contenu** de votre archive sur la bande « Actif multimédia », mais supprime seulement le « magnétophone » et arrête l’archivage. L’actif multimédia est toujours conservé avec le contenu vidéo archivé tant que vous n’appelez pas la fonction de suppression explicitement sur l’actif multimédia lui-même. Dès que vous supprimez la sortie en direct, le contenu enregistré de l’« actif multimédia » reste disponible pour la lecture via les URL de localisateur de streaming déjà publiées. Si vous voulez supprimer la possibilité pour un client de lire le contenu archivé, vous devez d’abord supprimer tous les localisateurs de l’actif multimédia et vider le cache CDN sur le chemin de l’URL si vous utilisez un CDN pour la distribution. Sinon, le contenu reste dans le cache du CDN pendant un temps déterminé par la valeur de la durée de vie standard sur le CDN (qui peut atteindre jusqu’à 72 heures).

#### <a name="create-a-streaming-locator-to-publish-hls-and-dash-manifests"></a>Créer un localisateur de streaming pour publier des manifestes HLS et DASH

> [!NOTE]
> Après la création de votre compte Media Services, un point de terminaison de streaming **par défaut** est ajouté à votre compte à l’état **Arrêté**. Pour démarrer le streaming de votre contenu et tirer parti de l’[empaquetage dynamique](encode-dynamic-packaging-concept.md) et du chiffrement dynamique, le point de terminaison de streaming à partir duquel vous souhaitez diffuser du contenu doit se trouver à l’état **En cours d’exécution**.

Quand vous publiez l’actif multimédia à l’aide d’un localisateur de streaming, l’événement en direct (jusqu’à la longueur de la fenêtre DVR) reste visible jusqu’à l’expiration ou la suppression du localisateur de streaming, en fonction de ce qui se produit en premier. C’est comme cela que vous pouvez mettre l’enregistrement sur « cassette virtuelle » à la disposition de votre public pour le regarder en direct et à la demande. La même URL peut être utilisée pour regarder l’événement en direct, la fenêtre DVR ou l’actif multimédia à la demande quand l’enregistrement est terminé (quand la sortie en direct est supprimée).

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateStreamingLocator)]

#### <a name="build-the-paths-to-the-hls-and-dash-manifests"></a>Créer les chemins aux manifestes HLS et DASH

La méthode BuildManifestPaths de l’exemple montre comment créer de façon déterministe les chemins de streaming à utiliser pour la distribution DASH ou HLS à différents clients et infrastructures de lecteur.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#BuildManifestPaths)]

## <a name="watch-the-event"></a>Regarder l’événement

Pour regarder l’événement, copiez l’URL de streaming obtenue à l’exécution du code décrit dans Créer un localisateur de streaming. Vous pouvez utiliser le lecteur multimédia de votre choix. [Lecteur multimédia Azure](https://amp.azure.net/libs/amp/latest/docs/index.html) vous permet de tester votre flux à l’adresse https://ampdemo.azureedge.net.

Une fois arrêté, l’événement en direct est automatiquement converti en contenu à la demande. Même après l’arrêt et la suppression de l’événement, les utilisateurs pourront lire votre contenu archivé en tant que vidéo à la demande tant que vous n’aurez pas supprimé l’élément multimédia. Vous ne pouvez pas supprimer d’élément multimédia s’il est utilisé par un événement. Vous devez d’abord supprimer l’événement.

### <a name="cleaning-up-resources-in-your-media-services-account"></a>Suppression des ressources de votre compte Media Services

Si vous exécutez l’application jusqu’au bout, elle va nettoyer automatiquement toutes les ressources utilisées dans la fonction appelée « cleanUpResources ». Vérifiez que l’application ou le débogueur s’exécute jusqu’au bout ; sinon, vous risquez de perdre des ressources et de vous retrouver avec des événements en direct dans votre compte. Vérifiez bien dans le portail Azure que toutes les ressources sont nettoyées dans votre compte Media Services.  

Dans l’exemple de code, reportez-vous à la méthode **cleanUpResources** pour plus d’informations.

> [!IMPORTANT]
> Laisser l’événement en direct s’exécuter entraîne des frais. Sachez que si le projet/programme plante ou est fermé pour une quelconque raison, l’événement peut continuer de s’exécuter et ainsi entraîner des frais.

## <a name="ask-questions-give-feedback-get-updates"></a>Poser des questions, envoyer des commentaires, obtenir des mises à jour

Découvrez l’article [Communauté Azure Media Services](media-services-community.md) pour découvrir les différentes façons dont vous pouvez poser des questions, faire des commentaires et obtenir des mises à jour sur Media Services.

## <a name="more-developer-documentation-for-nodejs-on-azure"></a>Documentation supplémentaire pour les développeurs pour Node.js sur Azure

- [Azure pour les développeurs JavaScript et Node.js](/azure/developer/javascript/)
- [Code source de Media Services dans le dépôt GitHub @azure/azure-sdk-for-js](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)
- [Documentation sur les packages Azure pour les développeurs Node.js](/javascript/api/overview/azure/)


## <a name="next-steps"></a>Étapes suivantes

[Diffuser des fichiers](stream-files-tutorial-with-api.md)