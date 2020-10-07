---
title: Démarrage rapide du Lecteur multimédia Azure
description: Découvrez les étapes de base de la configuration du Lecteur multimédia Azure.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: quickstart
ms.date: 04/20/2020
ms.openlocfilehash: ac81832765f674e58ad6b3213238e9c68e04d2dc
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2020
ms.locfileid: "81727476"
---
# <a name="azure-media-player-quickstart"></a>Démarrage rapide du Lecteur multimédia Azure
Le lecteur multimédia Azure est facile à configurer. Seules quelques minutes sont nécessaires pour récupérer la lecture simple de contenu multimédia à partir de votre compte Azure Media Services. Cette section montre les étapes de base sans entrer dans les détails. Les sections qui suivent fournissent des détails sur l’installation et la configuration du Lecteur multimédia Azure.  Ajoutez simplement ce qui suit à l’élément `<head>` de votre document :

```html
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <script src= "//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>
```

> [!IMPORTANT]
> Vous ne devez **PAS** utiliser la version `latest` en production, car elle est susceptible de changer à la demande. Remplacez `latest` par une version du Lecteur multimédia Azure. Par exemple, remplacez `latest` par `1.0.0`. Les versions du Lecteur multimédia Azure peuvent être examinées à partir d’[ici](azure-media-player-changelog.md).

## <a name="use-the-video-element"></a>Utiliser l’élément vidéo

Ensuite, utilisez juste l’élément `<video>` comme vous le feriez normalement, mais avec un attribut `data-setup` supplémentaire contenant des options. Ces options peuvent inclure n’importe quelle option Azure Media Services dans un objet JSON valide.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" autoplay controls width="640" height="400" poster="poster.jpg" data-setup='{"nativeControlsForTouch": false}'>
        <source src="http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest" type="application/vnd.ms-sstr+xml" />
        <p class="amp-no-js">
            To view this video please enable JavaScript, and consider upgrading to a web browser that supports HTML5 video
        </p>
    </video>
```

Si vous ne souhaitez pas utiliser la configuration automatique, vous pouvez omettre l’attribut `data-setup` et initialiser manuellement un élément vidéo.

```html
    var myPlayer = amp('vid1', { /* Options */
            "nativeControlsForTouch": false,
            autoplay: false,
            controls: true,
            width: "640",
            height: "400",
            poster: ""
        }, function() {
              console.log('Good to go!');
               // add an event listener
              this.addEventListener('ended', function() {
                console.log('Finished!');
            }
          }
    );
    myPlayer.src([{
        src: "http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
        type: "application/vnd.ms-sstr+xml"
    }]);
```

## <a name="next-steps"></a>Étapes suivantes ##

- [Démarrage rapide du Lecteur multimédia Azure](azure-media-player-quickstart.md)