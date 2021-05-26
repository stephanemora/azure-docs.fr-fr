---
title: Détection des effets audio
titleSuffix: Azure Video Analyzer
description: La détection des effets audio fait partie des fonctionnalités d’IA d’Azure Video Analyzer for Media. Elle peut détecter divers événements acoustiques et les classifier en différentes catégories (par exemple, coup de feu, cri, réaction de foule, etc.).
author: Juliako
manager: femila
ms.service: azure-video-analyzer
ms.topic: article
ms.date: 05/12/2021
ms.author: juliako
ms.openlocfilehash: 79bcde166be7dac53bc7c7d9d8e3ed5e0fc2f91a
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110386184"
---
#  <a name="audio-effects-detection-preview"></a>Détection des effets audio (préversion)

La **détection des effets audio** fait partie des fonctionnalités d’IA d’Azure Video Analyzer for Media. Elle peut détecter divers événements acoustiques et les classifier en différentes catégories (par exemple, coup de feu, cri, réaction de foule, etc.).
 
La détection des effets audio peut être utilisée dans de nombreux domaines. En voici deux exemples:

* L’utilisation de la détection des effets audio est réservée au domaine de la **sécurité publique et de la justice**. La détection des effets audio peut détecter et classifier des coups de feu, des explosions et des bris de verre. Par conséquent, elle peut être implémentée dans un système de ville intelligente ou dans d’autres environnements publics où sont installés des caméras et des micros. Offre une détection rapide et précise des violences. 
* Dans le domaine des **médias et du divertissement**, les entreprises disposant d’un grand nombre d’archives vidéo peuvent facilement améliorer leurs scénarios d’accessibilité. Pour cela, elles peuvent améliorer leur transcription vidéo avec des effets non vocaux afin de fournir davantage de contexte aux personnes malentendantes.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/audio-effects-detection/audio-effects.jpg" alt-text="Image des effets audio":::
<br/>*Exemple de sortie de la détection des effets audio multimédias dans Video Analyzer*

## <a name="supported-audio-categories"></a>Catégories audio prises en charge  

La **détection des effets audio** peut détecter et classifier les effets dans 8 catégories différentes. Dans le tableau suivant, vous pouvez voir les différentes catégories séparées selon les différents préréglages VI, et selon qu’elles sont **Standard** ou **Avancées**. Pour plus d’informations, voir la [tarification](https://azure.microsoft.com/pricing/details/media-services/).

|Type d’indexation |Indexation standard| Indexation avancée|
|---|---|---|
|**Nom du préréglage** |**Audio Only** (Audio uniquement) <br/>**Video + Audio** (Vidéo + Audio) |**Advance Audio** (Audio - Avancé)<br/> **Advance Video + Audio** (Vidéo + Audio - Avancé)|
|**Apparaître dans le volet des insights**|| V|
|Réaction de foule |V| V|
| Silence| V| V|
| Coup de feu ||V |
| Bris de verre ||V|
| Alarme|| V |
| Sirène|| V |
| Rire|| V |
| Aboiement|| V|

## <a name="result-formats"></a>Formats des résultats

Les effets audio sont récupérés dans le JSON des insights, qui comprend l’ID, le type et le nom de la catégorie, ainsi que l’ensemble d’instances par catégorie, avec la période et le score de confiance qui leur sont associés.

Le paramètre `name` est présenté dans le langage qui a été utilisé pour l’indexation du JSON, alors que le type reste toujours le même.

```json
audioEffects: [{
        id: 0,
        type: "Gunshot",
        name: "Gunshot",
        instances: [{
                confidence: 0.649,
                adjustedStart: "0:00:13.9",
                adjustedEnd: "0:00:14.7",
                start: "0:00:13.9",
                end: "0:00:14.7"
            }, {
                confidence: 0.7706,
                adjustedStart: "0:01:54.3",
                adjustedEnd: "0:01:55",
                start: "0:01:54.3",
                end: "0:01:55"
            }
        ]
    }, {
        id: 1,
        type: "CrowdReactions",
        name: "Crowd Reactions",
        instances: [{
                confidence: 0.6816,
                adjustedStart: "0:00:47.9",
                adjustedEnd: "0:00:52.5",
                start: "0:00:47.9",
                end: "0:00:52.5"
            },
            {
                confidence: 0.7314,
                adjustedStart: "0:04:57.67",
                adjustedEnd: "0:05:01.57",
                start: "0:04:57.67",
                end: "0:05:01.57"
            }
        ]
    }
],
```

## <a name="how-to-index-audio-effects"></a>Comment indexer des effets audio

Pour définir le processus d’indexation de manière à inclure la détection des effets audio, l’utilisateur doit choisir l’un des préréglages avancés sous le menu « Indexation vidéo + audio », comme illustré ci-dessous.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/audio-effects-detection/index-audio-effect.png" alt-text="Image de l’indexation des effets audio":::

## <a name="closed-caption"></a>Sous-titre

Lorsque des effets audio sont récupérés dans les fichiers de sous-titres, ils sont récupérés entre crochets selon la structure suivante :

|Type| Exemple|
|---|---|
|SRT |00:00:00,000  00:00:03,671<br/>[Coup de feu]|
|VTT |00:00:00.000  00:00:03.671<br/>[Coup de feu]|
|TTML|Score de confiance : 0.9047 <br/> <p begin="00:00:00.000" end="00:00:03.671">[Coup de feu]</p>|
|TXT |[Coup de feu]|
|CSV |0.9047,00:00:00.000,00:00:03.671, [Coup de feu]|

Les effets audio du fichier de sous-titres sont récupérés à l’aide de la logique suivante :

* Le type d’événement `Silence` ne sera pas ajouté aux sous-titres
* Durée maximale d’affichage d’un événement I 5 secondes
* La durée minimale du minuteur pour afficher un événement est de 700 millisecondes

## <a name="adding-audio-effects-in-closed-caption-files"></a>Ajout d’effets audio dans des fichiers de sous-titres

Vous pouvez ajouter des effets audio aux fichiers de sous-titres pris en charge par Azure Video Analyzer via l’[API Get video captions](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Captions)en choisissant True dans le paramètre `includeAudioEffects`, ou via l’expérience du portail video.ai en sélectionnant **Download** -> **Closed Captions** -> **Include Audio Effects** (Télécharger > Sous-titres > Inclure les effets audio).

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/audio-effects-detection/close-caption.jpg" alt-text="Effets audio dans les sous-titres":::

> [!NOTE]
> Lorsque vous utilisez [update transcript](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Video-Transcript) (mettre à jour la transcription) ou [update custom language model](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Language-Model) (mettre à jour le modèle de langage personnalisé) dans les fichiers de sous-titres, les effets audio inclus dans ces fichiers sont ignorés.

## <a name="limitations-and-assumptions"></a>Limitations et hypothèses

* Le modèle fonctionne uniquement sur les segments non vocaux.
* Le modèle fonctionne avec une seule catégorie à la fois. Par exemple, il ne prend pas en charge des pleurs et des paroles en arrière-plan, ou un coup de feu et une explosion.
* Il ne prend pas non plus en charge les cas où il y a de la musique forte en arrière-plan.
* Durée minimale du segment – 2 secondes.

## <a name="next-steps"></a>Étapes suivantes

Consulter la [vue d’ensemble](video-indexer-overview.md)
