---
title: Incorporer des widgets Video Indexer dans vos applications
titlesuffix: Azure Media Services
description: Découvrez comment incorporer des widgets Video Indexer dans vos applications.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: fe3466dcccf6381f26c823ce3deb2126c9534548
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60560427"
---
# <a name="embed-video-indexer-widgets-into-your-applications"></a>Incorporer des widgets Video Indexer dans vos applications

Cet article vous explique comment incorporer des widgets Video Indexer dans vos applications. Video Indexer prend en charge l'incorporation de deux types de widgets dans votre application : **Insights cognitifs** et **Lecteur**. 

À partir de la version 2, l'URL de base du widget inclut la région du compte. Par exemple, un compte situé dans la région USA Ouest génère : `https://wus2.videoindexer.ai/embed/insights/...`.

## <a name="widget-types"></a>Types de widgets

### <a name="cognitive-insights-widget"></a>Widget Insight cognitifs

Un widget **Insight cognitifs** inclut tous les insights visuels extraits à partir du processus d’indexation de votre vidéo. Le widget Insights prend en charge les paramètres d’URL facultatifs suivants :

|Nom|Définition|Description|
|---|---|---|
|widgets|Chaînes séparées par des virgules|Vous permet de contrôler les insights dont vous voulez faire le rendu. <br/>Exemple : `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,search` affichera uniquement les insights d’IU des marques (brands) et des personnes (people)<br/>Options disponibles : personnes (people), mots clés (keywords), annotations, marques (brands), sentiments, transcription (transcript), recherche (search).<br/>non pris en charge via l’URL dans la version=2<br/><br/>**Remarque :** Le paramètre d’URL de widgets n’est pas pris en charge dans la version 2. |

### <a name="player-widget"></a>Widget Lecteur

Un widget **Lecteur** vous permet de diffuser la vidéo en continu à l’aide d’une vitesse de transmission adaptative. Le widget Lecteur prend en charge les paramètres d’URL facultatifs suivants :

|Nom|Définition|Description|
|---|---|---|
|t|Secondes depuis le début|Fait démarrer le lecteur à partir d’un instant donné.<br/>Exemple : t=60|
|captions|Code de langue|Extrait les sous-titres dans la langue spécifiée pendant le chargement du widget pour les rendre disponibles dans le menu des sous-titres.<br/>Exemple : captions=en-US|
|showCaptions|Une valeur booléenne|Permet de charger le lecteur avec les sous-titres déjà activés.<br/>Exemple : showCaptions=true|
|Type||Active une apparence du lecteur audio (la partie vidéo est supprimée).<br/>Exemple : type=audio|
|autoplay|Une valeur booléenne|Indique si le lecteur doit commencer la lecture de la vidéo après le chargement (la valeur par défaut est true).<br/>Exemple : autoplay=false|
|Langage|Code de langue|Détermine la langue du lecteur (la valeur par défaut est en-US)<br/>Exemple : language=de-DE|

## <a name="embedding-public-content"></a>Incorporation de contenu public

1. Accédez au site web [Video Indexer](https://www.videoindexer.ai/) et connectez-vous.
2. Cliquez sur la vidéo que vous souhaitez utiliser.
3. Cliquez sur le bouton « Incorporer » qui s’affiche sous la vidéo.

    ![Widget](./media/video-indexer-embed-widgets/video-indexer-widget01.png)

    Après que vous avez cliqué sur le bouton, une fenêtre d’incorporation s’affiche sur l’écran, vous permettant de choisir le widget à incorporer dans votre application.
    La sélection d’un widget (**Lecteur** ou **Insight cognitifs**) génère le code incorporé afin que vous puissiez le coller dans votre application.
 
4. Choisissez le type de widget souhaité (**Insight cognitifs** ou **Lecteur**).
5. Copiez le code incorporé et ajoutez-le à votre application. 

    ![Widget](./media/video-indexer-embed-widgets/video-indexer-widget02.png)

## <a name="embedding-private-content"></a>Incorporation de contenu privé

Vous pouvez obtenir des codes incorporés à partir de fenêtres contextuelles d’incorporation (comme indiqué dans la section précédente) uniquement pour des vidéos **publiques**. 

Si vous souhaitez incorporer une vidéo **privée**, vous devez passer un jeton d’accès dans l’attribut **src** de **iframe** :

`https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>/?accessToken=<accessToken>`
    
Utilisez l’API [**Obtenir le widget Insight**](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) pour obtenir le contenu du widget Insight cognitifs, ou utilisez [**Obtenir un jeton d’accès vidéo**](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?) et ajoutez-le comme paramètre de requête à l’URL comme indiqué ci-dessus. Spécifiez cette URL comme la valeur **src** de **iframe**.

Si vous souhaitez fournir des fonctionnalités d’édition d’insights (comme nous l’avons dans notre application web) dans votre widget incorporé, vous devrez passer un jeton d’accès avec des autorisations de modification. Utilisez [**Obtenir le widget Insight**](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) ou [**Obtenir un jeton d’accès vidéo**](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?) avec **& allowEdit=true**. 

## <a name="widgets-interaction"></a>Interaction de widgets

Le widget **Insight cognitifs** peut interagir avec une vidéo sur votre application. Cette section montre comment réaliser cette interaction.

![Widget](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="cross-origin-communications"></a>Communications cross-origin

Pour que les widgets Video Indexer communiquent avec d’autres composants, le service Video Indexer effectue les opérations suivantes :

- Utilise la méthode de communication HTML5 cross-origin **postMessage** et 
- Valide le message vers l’origine VideoIndexer.ai. 

Si vous choisissez d’implémenter votre propre code de lecteur et d’effectuer l’intégration avec les widgets **Insight cognitifs**, il vous incombe de valider l’origine du message provenant de VideoIndexer.ai.

### <a name="embed-both-types-of-widgets-in-your-application--blog-recommended"></a>Incorporer les deux types de widgets dans votre application / blog (recommandé) 

Cette section montre comment faire interagir deux widgets Video Indexer, de sorte que le lecteur aille directement au moment approprié lorsqu’un utilisateur clique sur le contrôle d’insight de votre application.

`<script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>`

1. Copiez le code incorporé du widget **Lecteur**.
2. Copiez le code incorporé **Insight cognitifs**.
3. Ajoutez le [**fichier Médiateur**](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js) pour gérer la communication entre les deux widgets :

`<script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>`

Quand un utilisateur clique sur le contrôle d’insight sur votre application, le lecteur va maintenant directement au moment approprié.

Pour plus d’informations, consultez [cette démonstration](https://codepen.io/videoindexer/pen/NzJeOb).

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>Incorporer le widget Insight cognitifs et utiliser le lecteur multimédia Azure pour lire le contenu

Cette section montre comment obtenir une interaction entre un widget **Insight cognitifs** et une instance du lecteur multimédia Azure à l’aide du [plug-in AMP](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js).
 
1. Ajoutez un plug-in Video Indexer pour le lecteur AMP.<br/> `<script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>`
2. Instanciez le lecteur multimédia Azure avec le plug-in Video Indexer.

        // Init Source
        function initSource() {
            var tracks = [{
            kind: 'captions',
            // Here is how to load vtt from VI, you can replace it with your vtt url.
            src: this.getSubtitlesUrl("c4c1ad4c9a", "English"),
            srclang: 'en',
            label: 'English'
            }];

            myPlayer.src([
            {
                "src": "//amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
                "type": "application/vnd.ms-sstr+xml"
            }
            ], tracks);
        }

        // Init your AMP instance
        var myPlayer = amp('vid1', { /* Options */
            "nativeControlsForTouch": false,
            autoplay: true,
            controls: true,
            width: "640",
            height: "400",
            poster: "",
            plugins: {
            videobreakedown: {}
            }
        }, function () {
            // Activate the plugin
            this.videobreakdown({
            videoId: "c4c1ad4c9a",
            syncTranscript: true,
            syncLanguage: true
            });

            // Set the source dynamically
            initSource.call(this);
        });

3. Copiez le code incorporé **Insight cognitifs**.

Vous pourrez maintenant communiquer avec votre lecteur multimédia Azure.

Pour plus d’informations, consultez [cette démonstration](https://codepen.io/videoindexer/pen/rYONrO).

### <a name="embed-video-indexer-cognitive-insights-widget-and-use-your-own-player-could-be-any-player"></a>Incorporer le widget Insight cognitif de Video Indexer et utiliser votre propre lecteur (il peut s’agir de n’importe quel lecteur)

Si vous utilisez votre propre lecteur, vous devez vous-même prendre en charge la manipulation de votre lecteur pour optimiser la communication. 

1. Insérez votre lecteur vidéo.

    Par exemple, un lecteur HTML5 standard

        <video id="vid1" width="640" height="360" controls autoplay preload>
           <source src="//breakdown.blob.core.windows.net/public/Microsoft%20HoloLens-%20RoboRaid.mp4" type="video/mp4" /> 
           Your browser does not support the video tag.
        </video>    

2. Incorporez le widget Insight cognitifs.
3. Implémenter une communication pour votre lecteur en écoutant l’événement « message ». Par exemple : 

        <script>
    
            (function(){
            // Reference your player instance
            var playerInstance = document.getElementById('vid1');
        
            function jumpTo(evt) {
              var origin = evt.origin || evt.originalEvent.origin;
        
              // Validate that event comes from the videobreakdown domain.
              if ((origin === "https://www.videobreakdown.com") && evt.data.time !== undefined){
                
                // Here you need to call your player "jumpTo" implementation
                playerInstance.currentTime = evt.data.time;
               
                // Confirm arrival to us
                if ('postMessage' in window) {
                  evt.source.postMessage({confirm: true, time: evt.data.time}, origin);
                }
              }
            }
        
            // Listen to message event
            window.addEventListener("message", jumpTo, false);
          
            }())    
        
        </script>

Pour plus d’informations, consultez [cette démonstration](https://codepen.io/videoindexer/pen/YEyPLd).

## <a name="adding-subtitles"></a>Ajout de sous-titres

Si vous incorporez des insights de Video Indexer avec votre propre lecteur AMP, vous pouvez utiliser la méthode **GetVttUrl** pour obtenir des sous-titres codés (sous-titres). Vous pouvez également appeler une méthode javascript à partir du plug-in AMP de Video Indexer **getSubtitlesUrl** (comme indiqué précédemment). 

## <a name="customizing-embeddable-widgets"></a>Personnalisation des widgets intégrables

### <a name="cognitive-insights-widget"></a>Widget Insight cognitifs

Vous pouvez choisir les types d'insights souhaités en les spécifiant sous forme de valeur dans le paramètre d'URL suivant ajouté au code incorporé que vous obtenez (à partir de l'API ou de l'application web) : `&widgets=<list of wanted widgets>`.

Les valeurs possibles sont : personnes, mots clés, sentiments, transcription, recherche.

Par exemple, si vous souhaitez incorporer un widget contenant uniquement des insights de personnes et de recherche, l'URL incorporée d'iframe se présentera comme suit :

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,search`

Le titre de la fenêtre d'iframe peut également être personnalisé en ajoutant `&title=<YourTitle>` à l'URL d'iframe. (Cela personnalisera la valeur HTML \<title >).
    
Par exemple, si vous souhaitez donner à la fenêtre d'iframe le titre « MyInsights », l'URL se présentera comme suit :

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?title=MyInsights`

Notez que cette option n’est utile que les cas où vous avez besoin d’ouvrir les insights dans une nouvelle fenêtre.

### <a name="player-widget"></a>Widget Lecteur

Si vous incorporez le lecteur Video Indexer, vous pouvez choisir la taille du lecteur en spécifiant la taille de l’iframe.

Par exemple : 

`<iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/" frameborder="0" allowfullscreen />`

Par défaut, le lecteur par défaut Video Indexer disposera de sous-titres générés automatiquement en fonction de la transcription de la vidéo extraite avec la langue source sélectionnée lors du chargement de la vidéo.

Si vous souhaitez utiliser une autre langue pour l'incorporation, vous pouvez ajouter `&captions=< Language | ”all” | “false” >` à l'URL du lecteur incorporé. Vous pouvez également saisir la valeur « all » si vous souhaitez que les sous-titres de toutes les langues soient disponibles.
Si vous souhaitez que les sous-titres soient affichés par défaut, vous pouvez transmettre `&showCaptions=true`.

L'URL incorporée se présentera comme suit : 

`https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/?captions=italian`

Si vous souhaitez désactiver les sous-titres, vous pouvez passer « false » comme valeur pour les paramètres de sous-titre.

Lecture d’automatique : par défaut, le lecteur démarrera la lecture de la vidéo. Vous pouvez choisir de ne pas passer &autoplay=false dans l’URL d’incorporation ci-dessus.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la façon d’afficher et de modifier les insights de Video Indexer, consultez [cet](video-indexer-view-edit.md) article.

Découvrez également [Video indexer CodePen](https://codepen.io/videoindexer/pen/eGxebZ).
