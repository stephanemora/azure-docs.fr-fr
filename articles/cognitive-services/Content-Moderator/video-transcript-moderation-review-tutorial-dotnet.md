---
title: 'Tutoriel : Modérer des vidéos et des transcriptions dans .NET - Content Moderator'
titleSuffix: Azure Cognitive Services
description: Ce tutoriel explique comment concevoir une solution de modération de vidéos et de transcriptions complète à l’aide de la modération assistée par ordinateur et de la création de révisions pour une intervention humaine.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 2fbd4270221cb23a4f99a0f8155bb1de76472f31
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74976976"
---
# <a name="tutorial-video-and-transcript-moderation"></a>Tutoriel : Modération de vidéos et de transcriptions

Ce tutoriel explique comment créer une solution de modération de vidéos et de transcriptions complète avec la modération assistée par ordinateur et l’intégration d’une révision humaine.

Ce didacticiel vous explique les procédures suivantes :

> [!div class="checklist"]
> - Compresser la ou les vidéos d’entrée pour un traitement plus rapide
> - Modérer la vidéo pour obtenir des captures et des trames avec des insights
> - Utiliser des horodatages de trame pour créer des miniatures (images)
> - Soumettre des horodatages et des miniatures pour créer des révisions de vidéos
> - Convertir la reconnaissance vocale (transcription) de vidéos avec l’API d’indexeur multimédia
> - Modérer la transcription avec le service de modération de texte
> - Ajouter la transcription modérée à la révision de la vidéo

## <a name="prerequisites"></a>Prérequis

- Inscrivez-vous au site web [Outil de révision Content Moderator](https://contentmoderator.cognitive.microsoft.com/) et créez des étiquettes personnalisées. Si vous avez besoin d’aide pour cette étape, voir [Utiliser des étiquettes](Review-Tool-User-Guide/tags.md).

    ![Capture d’écran d’étiquettes personnalisées de modération de vidéos](images/video-tutorial-custom-tags.png)
- Pour exécuter l’exemple d’application, vous avez besoin d’un compte Azure, d’une ressource Azure Media Services, d’une ressource Azure Content Moderator et d’informations d’identification Azure Active Directory. Pour savoir comment obtenir ces ressources, consultez le guide [API Modération des vidéos](video-moderation-api.md).
- Téléchargez l’[application console de révision de vidéo](https://github.com/MicrosoftContentModerator/VideoReviewConsoleApp) à partir de GitHub.

## <a name="enter-credentials"></a>Entrer les informations d’identification

Modifiez le fichier `App.config` et ajoutez le nom du locataire Active Directory, les points de terminaison de service et les clés d’abonnement indiquées par `#####`. Les informations suivantes sont nécessaires :

|Clé|Description|
|-|-|
|`AzureMediaServiceRestApiEndpoint`|Point de terminaison pour l’API Azure Media Services (AMS)|
|`ClientSecret`|Clé d’abonnement pour Azure Media Services|
|`ClientId`|ID client pour Azure Media Services|
|`AzureAdTenantName`|Nom du locataire Active Directory représentant votre organisation|
|`ContentModeratorReviewApiSubscriptionKey`|Clé d’abonnement pour l’API de révision Content Moderator|
|`ContentModeratorApiEndpoint`|Point de terminaison pour l’API Content Moderator|
|`ContentModeratorTeamId`|ID d’équipe Content Moderator|

## <a name="examine-the-main-code"></a>Examinez le code principal

La classe `Program` de `Program.cs` est le point d’entrée principal de l’application de modération des vidéos.

### <a name="methods-of-program-class"></a>Méthodes de la classe Program

|Méthode|Description|
|-|-|
|`Main`|Analyse la ligne de commande, collecte l’entrée utilisateur et commence le traitement.|
|`ProcessVideo`|Compresse, charge, modère et crée des révisions de vidéos.|
|`CreateVideoStreamingRequest`|Crée un flux pour charger une vidéo.|
|`GetUserInputs`|Collecte l’entrée utilisateur ; utilisée quand aucune option de ligne de commande n’est présente.|
|`Initialize`|Initialise les objets nécessaires pour le processus de modération.|

### <a name="the-main-method"></a>La méthode Main

`Main()` est l’emplacement où commence l’exécution. C’est donc par ce point qu’il faut commencer pour comprendre le processus de modération des vidéos.

[!code-csharp[Main](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/Program.cs?range=20-24,42-52,54-74)]

`Main()` gère les arguments de ligne de commande suivants :

- Le chemin d’un répertoire contenant des fichiers vidéo MPEG-4 à soumettre à la modération. Tous les fichiers `*.mp4` présents dans ce répertoire et ses sous-répertoires sont soumis à la modération.
- Éventuellement, un indicateur booléen (true/false) spécifiant si les transcriptions de texte doivent être générées pour les besoins d’une modération de données audio.

Si aucun argument de ligne de commande n’est présent, `Main()` appelle `GetUserInputs()`. Cette méthode invite l’utilisateur à entrer le chemin d’un seul fichier vidéo et à spécifier si une transcription de texte doit être générée.

> [!NOTE]
> L’application console utilise l’[API Azure Media Indexer](https://docs.microsoft.com/azure/media-services/media-services-process-content-with-indexer2) pour générer des transcriptions à partir de la piste audio de la vidéo chargée. Les résultats sont fournis au format WebVTT. Pour plus d’informations sur ce format, consultez la rubrique relative au [format WebVTT (Web Video Text Tracks)](https://developer.mozilla.org/en-US/docs/Web/API/WebVTT_API).

### <a name="initialize-and-processvideo-methods"></a>Méthodes Initialize et ProcessVideo

Que les options du programme proviennent de la ligne de commande ou d’une entrée utilisateur interactive, `Main()` appelle ensuite `Initialize()` pour créer les instances suivantes :

|Classe|Description|
|-|-|
|`AMSComponent`|Compresse les fichiers vidéo avant de les soumettre à la modération.|
|`AMSconfigurations`|Interface pour les données de configuration de l’application, se trouvant dans `App.config`.|
|`VideoModerator`| Chargement, encodage, chiffrement et modération à l’aide du kit SDK AMS|
|`VideoReviewApi`|Gère les révisions des vidéos dans le service Content Moderator|

Ces classes (hormis `AMSConfigurations`, qui est simple) sont traitées plus en détail dans les prochaines sections de ce tutoriel.

Enfin, les fichiers vidéo sont traités un par un en appelant `ProcessVideo()` pour chacun.

[!code-csharp[ProcessVideo](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/Program.cs?range=76-118)]

La méthode `ProcessVideo()` est assez simple. Elle effectue les opérations suivantes, dans l’ordre :

- Compresse la vidéo
- Charge la vidéo dans une ressource Azure Media Services
- Crée une tâche AMS pour modérer la vidéo
- Crée une révision de vidéo dans Content Moderator

Les sections suivantes examinent plus en détail certains des différents processus appelés par `ProcessVideo()`. 

## <a name="compress-the-video"></a>Compresser la vidéo

Pour réduire le trafic réseau, l’application convertit les fichiers vidéo au format H.264 (MPEG-4 AVC) et les met à l’échelle à une largeur maximale de 640 pixels. Le codec H.264 est recommandé en raison de sa haute efficacité (taux de compression). La compression est effectuée à l’aide de l’outil en ligne de commande gratuit `ffmpeg`, qui est inclus dans le dossier `Lib` de la solution Visual Studio. Les fichiers d’entrée peuvent être de n’importe quel format pris en charge par `ffmpeg`, ce qui inclut les codecs et formats de fichiers vidéo es plus fréquemment utilisés.

> [!NOTE]
> Quand vous démarrez le programme à l’aide d’options de ligne de commande, vous spécifiez un répertoire contenant les fichiers vidéo à soumettre à la modération. Tous les fichiers de ce répertoire ayant l’extension de nom de fichier `.mp4` sont traités. Pour traiter d’autres extensions de nom de fichier, mettez à jour la méthode `Main()` dans `Program.cs` pour inclure les extensions souhaitées.

Le code qui compresse un seul fichier vidéo est la classe `AmsComponent` dans `AMSComponent.cs`. La méthode chargée de cette fonctionnalité est `CompressVideo()`, illustrée ici.

[!code-csharp[CompressVideo](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/AMSComponent.cs?range=31-59)]

Le code effectue les actions suivantes :

- Vérifie que la configuration dans `App.config` contient toutes les données nécessaires
- Vérifie que le binaire `ffmpeg` est présent
- Génère le nom de fichier de sortie en ajoutant `_c.mp4` au nom de base du fichier (comme `Example.mp4` -> `Example_c.mp4`)
- Génère une chaîne de ligne de commande pour effectuer la conversion
- Démarre un processus `ffmpeg` à l’aide de la ligne de commande
- Attend que la vidéo soit traitée

> [!NOTE]
> Si vous savez que vos vidéos sont déjà compressées avec H.264 et que vous avez les dimensions appropriées, vous pouvez réécrire `CompressVideo()` pour ignorer la compression.

La méthode retourne le nom de fichier du fichier de sortie compressé.

## <a name="upload-and-moderate-the-video"></a>Charger et modérer la vidéo

La vidéo doit être stockée dans Azure Media Services pour pouvoir être traitée par le service Content Moderation. La classe `Program` dans `Program.cs` a une courte méthode `CreateVideoStreamingRequest()` qui retourne un objet représentant la requête de streaming utilisée pour charger la vidéo.

[!code-csharp[CreateVideoStreamingRequest](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/Program.cs?range=120-133)]

L’objet `UploadVideoStreamRequest` obtenu est défini dans `UploadVideoStreamRequest.cs` (et son parent, `UploadVideoRequest`, dans `UploadVideoRequest.cs`). Ces classes ne sont pas affichées ici ; elles sont brèves et servent uniquement à contenir les données vidéo compressées et des informations les concernant. Une autre classe de données uniquement, `UploadAssetResult` (`UploadAssetResult.cs`) est utilisée pour contenir les résultats du processus de chargement. Il est désormais possible de comprendre les lignes suivantes dans `ProcessVideo()` :

[!code-csharp[ProcessVideoSnippet](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/Program.cs?range=91-104)]

Ces lignes effectuent les tâches suivantes :

- Créent un `UploadVideoStreamRequest` pour charger la vidéo compressée
- Définissent l’indicateur `GenerateVTT` de la requête si l’utilisateur a demandé une transcription de texte
- Appellent `CreateAzureMediaServicesJobToModerateVideo()` pour effectuer le chargement et recevoir le résultat

## <a name="examine-video-moderation-code"></a>Examiner le code de modération de vidéo

La méthode `CreateAzureMediaServicesJobToModerateVideo()` se trouve dans `VideoModerator.cs`, qui contient la majeure partie du code qui interagit avec Azure Media Services. Le code source de la méthode est présenté dans l’extrait suivant.

[!code-csharp[CreateAzureMediaServicesJobToModerateVideo](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoModerator.cs?range=230-283)]

Ce code effectue les tâches suivantes :

- Crée une tâche AMS pour le traitement à effectuer
- Ajoute des tâches pour l’encodage du fichier vidéo, sa modération et la génération d’une transcription de texte
- Soumet la tâche, en chargeant le fichier et en commençant le traitement
- Récupère les résultats de la modération, la transcription de texte (si nécessaire) et d’autres informations

## <a name="sample-video-moderation-output"></a>Exemple de sortie de modération de vidéo

Le résultat de la tâche de modération des vidéos (consultez le guide de [démarrage rapide de la modération des vidéos](video-moderation-api.md) est une structure de données JSON contenant les résultats de la modération. Ces résultats incluent une répartition des fragments (captures) dans la vidéo, chacun contenant des événements (clips) avec des trames clés qui ont été marquées pour révision. Chaque trame clé est évaluée selon la probabilité qu’elle contienne du contenu pour adultes ou osé. L’exemple suivant présent une réponse JSON :

```json
{
    "version": 2,
    "timescale": 90000,
    "offset": 0,
    "framerate": 50,
    "width": 1280,
    "height": 720,
    "totalDuration": 18696321,
    "fragments": [
    {
        "start": 0,
        "duration": 18000
    },
    {
        "start": 18000,
        "duration": 3600,
        "interval": 3600,
        "events": [
        [
        {
            "reviewRecommended": false,
            "adultScore": 0.00001,
            "racyScore": 0.03077,
            "index": 5,
            "timestamp": 18000,
            "shotIndex": 0
        }
        ]
    ]
    },
    {
        "start": 18386372,
        "duration": 119149,
        "interval": 119149,
        "events": [
        [
        {
            "reviewRecommended": true,
            "adultScore": 0.00000,
            "racyScore": 0.91902,
            "index": 5085,
            "timestamp": 18386372,
            "shotIndex": 62
        }
    ]
    ]
    }
]
}
```

Une transcription des données audio de la vidéo est également générée quand l’indicateur `GenerateVTT` est défini.

> [!NOTE]
> L’application console utilise l’[API Azure Media Indexer](https://docs.microsoft.com/azure/media-services/media-services-process-content-with-indexer2) pour générer des transcriptions à partir de la piste audio de la vidéo chargée. Les résultats sont fournis au format WebVTT. Pour plus d’informations sur ce format, consultez la rubrique relative au [format WebVTT (Web Video Text Tracks)](https://developer.mozilla.org/en-US/docs/Web/API/WebVTT_API).

## <a name="create-a-human-review"></a>Créer une révision humaine

Le processus de modération retourne une liste de trames clés à partir de la vidéo, ainsi qu’une transcription de ses pistes audio. L’étape suivante consiste à créer une révision dans l’outil de révision Content Moderator pour les modérateurs humains. Concernant la méthode `ProcessVideo()` dans `Program.cs`, vous voyez l’appel à la méthode `CreateVideoReviewInContentModerator()`. Cette méthode, illustrée ici, se trouve dans la classe `videoReviewApi`, qui est dans `VideoReviewAPI.cs`.

[!code-csharp[CreateVideoReviewInContentModerator](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoReviewAPI.cs?range=42-69)]

`CreateVideoReviewInContentModerator()` appelle plusieurs autres méthodes pour effectuer les tâches suivantes :

> [!NOTE]
> L’application console utilise la bibliothèque [FFmpeg](https://ffmpeg.org/) pour générer des miniatures. Ces miniatures (images) correspondent aux horodatages de trame dans la sortie de la modération des vidéos.

|Tâche|Méthodes|Fichier|
|-|-|-|
|Extraire les trames clés à partir de la vidéo et créer des images miniatures de celles-ci|`CreateVideoFrames()`<br>`GenerateFrameImages()`|`FrameGeneratorServices.cs`|
|Analyser la transcription de texte, le cas échéant, pour localiser du contenu audio pour adultes ou osé|`GenerateTextScreenProfanity()`| `VideoReviewAPI.cs`|
|Préparer et soumettre une requête de révision de vidéo pour l’inspection par un opérateur humain|`CreateReviewRequestObject()`<br> `ExecuteCreateReviewApi()`<br>`CreateAndPublishReviewInContentModerator()`|`VideoReviewAPI.cs`|

L’écran suivant montre les résultats des étapes précédentes.

![Vue par défaut de la révision de vidéo](images/video-tutorial-default-view.PNG)

## <a name="process-the-transcript"></a>Explorer la transcription

Jusqu’à présent, le code présenté dans ce tutoriel se concentre sur le contenu visuel. La révision de contenu vocal est un processus distinct et facultatif qui, comme indiqué, utilise une transcription générée à partir des données audio. Il est maintenant temps d’examiner la façon dont les transcriptions de texte sont créées et utilisées dans le processus de révision. La tâche de génération de la transcription incombe au service [Azure Media Indexer](https://docs.microsoft.com/azure/media-services/media-services-index-content).

L’application effectue les tâches suivantes :

|Tâche|Méthodes|Fichier|
|-|-|-|
|Déterminer si des transcriptions de texte doivent être générées|`Main()`<br>`GetUserInputs()`|`Program.cs`|
|Si c’est le cas, soumettre une tâche de transcription dans le cadre de la modération|`ConfigureTranscriptTask()`|`VideoModerator.cs`|
|Obtenir une copie locale de la transcription|`GenerateTranscript()`|`VideoModerator.cs`|
|Marquer les trames de la vidéo qui contiennent des données audio inappropriées|`GenerateTextScreenProfanity()`<br>`TextScreen()`|`VideoReviewAPI.cs`|
|Ajouter les résultats à la révision|`UploadScreenTextResult()`<br>`ExecuteAddTranscriptSupportFile()`|`VideoReviewAPI.cs`|

### <a name="task-configuration"></a>Configuration de la tâche

Passons directement à la soumission de la tâche de transcription. `CreateAzureMediaServicesJobToModerateVideo()` (déjà décrite) appelle `ConfigureTranscriptTask()`.

[!code-csharp[ConfigureTranscriptTask](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoModerator.cs?range=295-304)]

La configuration de la tâche de transcription est lue dans le fichier `MediaIndexerConfig.json` situé dans le dossier `Lib` de la solution. Les ressources AMS sont créées pour le fichier de configuration et pour la sortie du processus de transcription. Quand la tâche AMS s’exécute, elle crée une transcription de texte à partir de la piste audio du fichier vidéo.

> [!NOTE]
> L’exemple d’application procède à la reconnaissance vocale en anglais américain uniquement.

### <a name="transcript-generation"></a>Génération de la transcription

La transcription est publiée sous la forme d’une ressource AMS. Pour analyser la transcription de contenu répréhensible, l’application télécharge la ressource à partir d’Azure Media Services. `CreateAzureMediaServicesJobToModerateVideo()` appelle `GenerateTranscript()`, illustrée ici, pour récupérer le fichier.

[!code-csharp[GenerateTranscript](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoModerator.cs?range=351-370)]

Après une configuration nécessaire d’AMS, le téléchargement réel s’effectue en appelant `DownloadAssetToLocal()`, une fonction générique qui copie une ressource AMS dans un fichier local.

## <a name="moderate-the-transcript"></a>Modérer la transcription

Avec la transcription à proximité, elle est analysée et utilisée dans la révision. La création de la révision est du ressort de `CreateVideoReviewInContentModerator()`, qui appelle `GenerateTextScreenProfanity()` pour effectuer cette opération. À son tour, cette méthode appelle `TextScreen()`, qui contient la plupart des fonctionnalités.

`TextScreen()` effectue les tâches suivantes :

- Analyser la transcription pour rechercher les horodatages et les sous-titres
- Soumettre chaque sous-titre à la modération de texte
- Indiquer toutes les trames qui peuvent avoir du contenu vocal répréhensible

Examinons plus en détail chacune de ces tâches :

### <a name="initialize-the-code"></a>Initialiser le code

Tout d’abord, initialisez toutes les variables et collections.

[!code-csharp[TextScreen](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoReviewAPI.cs?range=515-527)]

### <a name="parse-the-transcript-for-captions"></a>Analyser la transcription pour rechercher les sous-titres

Ensuite, analysez la transcription au format VTT pour rechercher les sous-titres et les horodatages. L’outil de révision affiche ces sous-titres sous l’onglet Transcription sur l’écran de révision des vidéos. Les horodatages sont utilisés pour synchroniser les sous-titres avec les trames vidéo correspondantes.

[!code-csharp[TextScreen2](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoReviewAPI.cs?range=528-567)]

### <a name="moderate-captions-with-the-text-moderation-service"></a>Modérer les sous-titres avec le service de modération de texte

Ensuite, nous analysons les sous-titres de texte décryptés avec l’API de texte de Content Moderator.

> [!NOTE]
> Votre clé de service Content Moderator a une limite de fréquence des demandes par seconde (RPS). Si vous dépassez la limite, le kit SDK lève une exception avec le code d’erreur 429.
>
> Une clé de niveau gratuit a une limite de fréquence d’une demande par seconde (RSP).

[!code-csharp[TextScreen3](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoReviewAPI.cs?range=568-653)]

### <a name="text-moderation-breakdown"></a>Décomposition de la modération de texte

`TextScreen()` est une méthode importante. Nous allons donc la décomposer.

1. Tout d’abord, la méthode lit le fichier de transcription ligne par ligne. Elle ignore les lignes vides et les lignes contenant une valeur `NOTE` avec un score de confiance. Elle extrait les horodatages et les éléments de texte à partir des *répliques* contenues dans le fichier. Une réplique représente le texte de la piste audio et inclut les heures de début et de fin. Une réplique commence à la ligne d’horodatage comprenant la chaîne `-->`. Elle est suivie d’une ou de plusieurs lignes de texte.

1. Les instances de `CaptionScreentextResult` (définies dans `TranscriptProfanity.cs`) sont utilisées pour contenir les informations analysées à partir de chaque réplique.  Quand une nouvelle ligne d’horodatage est détectée, ou qu’une longueur de texte maximale de 1 024 caractères est atteinte, un nouveau `CaptionScreentextResult` est ajouté au `csrList`. 

1. La méthode soumet ensuite chaque réplique à l’API Modération de texte. Elle appelle `ContentModeratorClient.TextModeration.DetectLanguageAsync()` et `ContentModeratorClient.TextModeration.ScreenTextWithHttpMessagesAsync()`, qui sont définies dans l’assembly `Microsoft.Azure.CognitiveServices.ContentModerator`. Pour éviter d’avoir une fréquence limitée, la méthode fait une pause d’une seconde avant de soumettre chaque réplique.

1. Après avoir reçu les résultats du service de modération de texte, la méthode les analyse pour voir s’ils atteignent les seuils de confiance. Ces valeurs sont établies dans `App.config` en tant que `OffensiveTextThreshold`, `RacyTextThreshold` et `AdultTextThreshold`. Enfin, les termes choquants sont eux-mêmes également stockés. Toutes les trames dans l’intervalle de temps de la réplique sont marquées comme contenant du texte offensant, osé et/ou pour adultes.

1. `TextScreen()` retourne une instance de `TranscriptScreenTextResult` qui contient le résultat de la modération du texte provenant de la vidéo dans son ensemble. Cet objet contient des indicateurs et des scores pour les différents types de contenus répréhensibles, ainsi qu’une liste de tous les termes choquants. L’appelant, `CreateVideoReviewInContentModerator()`, appelle `UploadScreenTextResult()` pour lier ces informations à la révision afin qu’elles soient disponible pour les réviseurs humains.

L’écran suivant montre le résultat de la génération de la transcription et les étapes de la modération.

![Vue de transcription de la modération de vidéo](images/video-tutorial-transcript-view.PNG)

## <a name="program-output"></a>Sortie du programme

La sortie de la ligne de commande suivante du programme montre les différentes tâches à mesure qu’elles sont terminées. De plus, le résultat de la modération (au format JSON) et la transcription vocale sont disponibles dans le même répertoire que les fichiers vidéo d’origine.

```console
Microsoft.ContentModerator.AMSComponentClient
Enter the fully qualified local path for Uploading the video :
"Your File Name.MP4"
Generate Video Transcript? [y/n] : y

Video compression process started...
Video compression process completed...

Video moderation process started...
Video moderation process completed...

Video review process started...
Video Frames Creation inprogress...
Frames(83) created successfully.
Review Created Successfully and the review Id 201801va8ec2108d6e043229ba7a9e6373edec5
Video review successfully completed...

Total Elapsed Time: 00:05:56.8420355
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez configuré une application qui modère le contenu vidéo&mdash;y compris le contenu de la transcription&mdash;et crée des révisions dans l’outil de révision. À présent, découvrez les détails de la modération de vidéo.

> [!div class="nextstepaction"]
> [Modération de vidéo](./video-moderation-human-review.md)
