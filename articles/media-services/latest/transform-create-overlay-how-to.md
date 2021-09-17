---
title: Comment créer une superposition d’image
description: Découvrez comment créer une superposition d’image
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 08/31/2020
ms.openlocfilehash: 532e26e486e758b3fd8079f6f61f3e00e82dbdca
ms.sourcegitcommit: c2f0d789f971e11205df9b4b4647816da6856f5b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122662300"
---
# <a name="how-to-create-an-image-overlay"></a>Comment créer une superposition d’image

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Media Services vous permet de superposer une image, un fichier audio ou une autre vidéo sur une vidéo. L’entrée doit spécifier exactement un fichier image. Vous pouvez spécifier un fichier image au format JPG, PNG, GIF ou BMP, un fichier audio (par exemple un fichier WAV, MP3, WMA ou M4A), ou un fichier vidéo dans un format de fichier pris en charge.


## <a name="prerequisites"></a>Conditions préalables requises

* Collectez les informations de compte dont vous avez besoin pour configurer le fichier *appsettings.json* dans l’exemple. Si vous n’êtes pas sûr de savoir comment procéder, consultez [Démarrage rapide : Inscrire une application avec la plateforme des identités Microsoft](../../active-directory/develop/quickstart-register-app.md). Les valeurs suivantes sont attendues dans le fichier *appsettings.json*.

```json
    {
    "AadClientId": "",
    "AadEndpoint": "https://login.microsoftonline.com",
    "AadSecret": "",
    "AadTenantId": "",
    "AccountName": "",
    "ArmAadAudience": "https://management.core.windows.net/",
    "ArmEndpoint": "https://management.azure.com/",
    "Location": "",
    "ResourceGroup": "",
    "SubscriptionId": ""
    }
```

Si vous n’êtes pas encore familiarisé avec la création de transformations, il est recommandé d’effectuer les activités suivantes :

* Lisez [Encodage vidéo et audio avec Media Services](encode-concept.md).
* Lisez [Comment encoder avec une transformation personnalisée – .NET](transform-custom-presets-how-to.md). Suivez les étapes de cet article pour configurer le .NET nécessaire à l’utilisation des transformations, puis revenez ici pour tester un exemple de présélection de superpositions.
* Consultez le [document de référence sur les transformations](/rest/api/media/transforms).

Une fois que vous êtes familiarisé avec les transformations, téléchargez l’exemple de superpositions.

## <a name="overlays-preset-sample"></a>Exemple de présélection de superpositions

Clonez l’exemple de référentiel Media Services .NET.

```bash
    git clone https://github.com/Azure-Samples/media-services-v3-dotnet.git
```

Accédez au dossier de la solution, puis lancez Visual Studio Code ou Visual Studio 2019.

Plusieurs exemples d’encodage sont disponibles dans le dossier VideoEncoding. Ouvrez le projet dans le dossier de la solution [VideoEncoding/Encoding_OverlayImage](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_OverlayImage) pour commencer à apprendre à utiliser les superpositions.

L’exemple de projet contient deux fichiers multimédias. Un fichier vidéo et une image de logo à superposer sur la vidéo.
* ignite.mp4
* cloud.png

Dans cet exemple, nous créons d’abord une transformation personnalisée qui peut superposer l’image sur la vidéo dans la méthode CreateCustomTransform.  À l’aide de la propriété *[Filtres](/rest/api/media/transforms/create-or-update#filters)* de *[StandardEncoderPreset](/rest/api/media/transforms/create-or-update#standardencoderpreset)* , nous attribuons une nouvelle collection Filtres qui contient les paramètres de superposition vidéo.

Une [VideoOverlay](/rest/api/media/transforms/create-or-update#videooverlay) contient une propriété appelée *InputLabel* qui est nécessaire pour mapper à partir de la liste des fichiers d’entrée de travail envoyés dans le travail et localiser le fichier source d’entrée approprié destiné à être utilisé comme vidéo ou image de superposition.  Lors de la soumission du travail, le même nom d’étiquette est utilisé pour correspondre au paramètre ici dans la transformation. Dans l’exemple, nous utilisons le nom d’étiquette « logo » comme indiqué dans la constante de chaîne *OverlayLabel*.

L’extrait de code suivant montre comment la transformation est mise en forme pour utiliser une superposition.

```csharp
new TransformOutput
                {
                    Preset = new StandardEncoderPreset
                    {
                        Filters = new Filters
                        {
                            Overlays = new List<Overlay>
                            {
                                new VideoOverlay
                                {
                                    InputLabel = OverlayLabel,   // same as the one used in the JobInput to identify which asset is the overlay image
                                    Position = new Rectangle( "1200","670") // left, top position of the overlay in absolute pixel position relative to the source videos resolution. 
    
                                }
                            }
                        },
                        Codecs = new List<Codec>
                        {
                            new AacAudio
                            {
                            },
                            new H264Video
                            {
                                KeyFrameInterval = TimeSpan.FromSeconds(2),
                                Layers = new List<H264Layer>
                                {
                                    new H264Layer
                                    {
                                        Profile = H264VideoProfile.Baseline,
                                        Bitrate = 1000000, // 1Mbps
                                        Width = "1280",
                                        Height = "720"
                                    },
                                    new H264Layer   // Adding a second layer to see that the image also is scaled and positioned the same way on this layer. 
                                    {
                                        Profile = H264VideoProfile.Baseline,
                                        Bitrate = 600000, // 600 kbps
                                        Width = "480",
                                        Height = "270"
                                    }
                                }
                            }
                        },
                        Formats = new List<Format>
                        {
                            new Mp4Format
                            {
                                FilenamePattern = "{Basename}_{Bitrate}{Extension}",
                            }
                        }
                    }
                }
```

Lors de l’envoi du travail à la transformation, vous devez d’abord créer les deux ressources d’entrée.

* Ressource 1 : dans cet exemple, la première ressource créée est le fichier vidéo local « ignite.mp4 ». Il s’agit de la vidéo que nous allons utiliser comme arrière-plan du composite et sur laquelle nous superposerons une image de logo. 
* Ressource 2 : dans cet exemple, la deuxième ressource (stockée dans la variable overlayImageAsset) contient le fichier .png à utiliser pour le logo. Cette image sera positionnée sur la vidéo pendant l’encodage.

Lorsque le travail est créé dans la méthode *SubmitJobAsync*, nous construisons d’abord un tableau JobInput en utilisant un objet List<>.  La liste contient les références aux deux ressources sources.

Afin d’identifier et de faire correspondre la ressource d’entrée à utiliser comme superposition dans le filtre défini dans la transformation ci-dessus, nous utilisons à nouveau le nom d’étiquette « logo » pour gérer la correspondance. Le nom de l’étiquette est ajouté à JobInputAsset pour l’image .png. Cela indique à la transformation la ressource à utiliser lors de l’opération de superposition. Vous pouvez réutiliser cette même transformation avec différentes ressources stockées dans Media Services qui contiennent divers logos ou images que vous souhaitez superposer, et simplement changer le nom de la ressource transmise dans le travail, tout en utilisant le même nom d’étiquette « logo » pour que la transformation puisse faire la correspondance.

``` csharp
    // Add both the Video and the Overlay image assets here as inputs to the job.
    List<JobInput> jobInputs = new List<JobInput>() {
        new JobInputAsset(assetName: inputAssetName),
        new JobInputAsset(assetName: overlayAssetName, label: OverlayLabel)
    };
```

Exécutez l’exemple en sélectionnant le projet dans la fenêtre Exécuter et déboguer dans Visual Studio Code. L’exemple affiche la progression de l’opération d’encodage et, enfin, télécharge le contenu dans le dossier/Output à la racine de votre projet ou, dans le cas d’une instance Visual Studio complète, dans votre dossier/bin/Output. 

L’exemple publie également le contenu pour la diffusion en continu et génère les URL complètes des fichiers manifestes TLS, DASH et Smooth Streaming qui peuvent être utilisés dans n’importe quel lecteur compatible.  Vous pouvez également copier facilement l’URL du manifeste dans la [démo Lecteur multimédia Azure](http://ampdemo.azureedge.net/) et coller l’URL qui se termine par /manifest dans la zone URL, puis cliquer sur *Mettre à jour le lecteur*.

## <a name="api-references"></a>Informations de référence des API

* [Objet VideoOverlay](/rest/api/media/transforms/create-or-update#videooverlay)
* [Filtres](/rest/api/media/transforms/create-or-update#filters)
* [StandardEncoderPreset](/rest/api/media/transforms/create-or-update#standardencoderpreset)


[!INCLUDE [reference dotnet sdk references](./includes/reference-dotnet-sdk-references.md)]

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [transforms next steps](./includes/transforms-next-steps.md)]
