---
title: Rechercher et éditer des visages dans l’API Azure Media Services v3 | Microsoft Docs
description: Azure Media Services v3 fournit un préréglage de détection et d’édition (floutage) de visage. Ainsi, vous pouvez soumettre un fichier vidéo, détecter des visages et éventuellement les éditer (flouter) en une seule passe ou via une opération en deux phases permettant d’apporter des modifications. Cet article montre comment rechercher et éditer des visages avec le préréglage de Face Detector dans l’API v3.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/25/2021
ms.author: johndeu
ms.custom: devx-track-csharp
ms.openlocfilehash: 4907a81fc8cb55499fa97f2b02a3e19e7117bbbc
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286383"
---
# <a name="find-and-redact-blur-faces-with-the-face-detector-preset"></a>Rechercher et éditer (flouter) des visages avec le préréglage de Face Detector

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

L’API Azure Media Services v3 comprend un préréglage de Face Detector, qui offre une fonctionnalité scalable de détection et d’édition (floutage) des visages dans le cloud. La rédaction de face vous permet de modifier votre vidéo afin de flouter les visages des individus sélectionnés. Vous souhaitez peut-être utiliser le service de rédaction de face dans des scénarios de média et de sécurité publics. Quelques minutes de séquences vidéo contenant plusieurs visages peuvent demander des heures d’édition manuelle. Toutefois, avec ce préréglage, le processus d’édition des visages nécessite juste quelques étapes simples.

Cet article apporte des précisions sur le **préréglage de Face Detector**, et explique comment l’utiliser avec le kit SDK Azure Media Services pour .NET.

[!INCLUDE [regulation](../video-indexer/includes/regulation.md)]

## <a name="compliance-privacy-and-security"></a>Conformité, confidentialité et sécurité
 
N’oubliez jamais que vous devez vous conformer à la réglementation applicable durant l’utilisation des analyses dans Azure Media Services. Vous ne devez pas utiliser Azure Media Services ou tout autre service Azure d’une manière qui ne respecte pas les droits d’autrui. Avant de charger des vidéos, en particulier des données biométriques, vers le service Azure Media Services à des fins de traitement et de stockage, vous devez disposer de tous les droits appropriés sur ces vidéos, notamment le consentement des personnes qui y figurent. Pour en savoir plus sur la conformité, la confidentialité et la sécurité dans Azure Media Services, consultez les [Conditions d’utilisation de Cognitive Services](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/). Pour connaître les obligations de Microsoft en matière de confidentialité et de traitement de vos données, consultez la [Déclaration de confidentialité](https://privacy.microsoft.com/PrivacyStatement), les [Conditions des Services en Ligne](https://www.microsoft.com/licensing/product-licensing/products) (OST) et l’[Addenda au traitement des données](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) (« DPA ») de Microsoft. Des informations complémentaires sur la confidentialité, notamment sur la conservation, la suppression et la destruction des données, sont disponibles dans l’OST et [ici](../video-indexer/faq.md). En utilisant Azure Media Services, vous acceptez d’être lié par les Conditions d’utilisation de Cognitive Services, à l’OST, au DPA et à la Déclaration de confidentialité

## <a name="face-redaction-modes"></a>Modes de rédaction de face

La rédaction de face fonctionne en détectant les visages dans chaque image de la vidéo et en suivant l’objet de visage à la fois vers l’avant et l’arrière dans le temps, afin que la même personne puisse être floutée à partir d’autres angles également. Le processus d’édition automatisée est complexe et ne floute pas toujours à 100 % tous les visages. Ainsi, le préréglage peut être utilisé dans un mode à deux passes afin d’améliorer la qualité et la justesse du floutage via une phase de modification préalable à la soumission du fichier pour la passe finale de floutage. 

En plus d’un mode **Combined** entièrement automatique, le workflow de travail en deux passes vous permet de choisir les visages que vous souhaitez flouter (ou non) via une liste d’ID de visage. Pour permettre l’exécution d’ajustements arbitraires par image, le préréglage utilise un fichier de métadonnées au format JSON en tant qu’entrée de la deuxième passe. Ce flux de travail est divisé en modes **Analyser** et **Rédiger**.

Vous pouvez également facilement combiner les deux modes en une seule passe pour exécuter les deux tâches au cours d’un seul travail. Il s’agit du mode **Combined**.  Dans cet article, l’exemple de code montre comment utiliser le mode **Combined** simplifié en une seule passe sur un exemple de fichier source.

### <a name="combined-mode"></a>Mode Combiné

Cela permet d’obtenir un fichier vidéo MP4 édité en une seule passe sans nécessiter de modification manuelle du fichier JSON. La sortie présente dans le dossier d’actifs multimédias du travail est un fichier .mp4 contenant des visages floutés à l’aide de l’effet de floutage sélectionné. Affectez à la propriété resolution la valeur **SourceResolution** pour obtenir les meilleurs résultats d’édition.

| Étape | Nom de fichier | Notes |
| --- | --- | --- |
| Élément multimédia d’entrée |"ignite-sample.mp4" |Vidéo au format WMV, MOV ou MP4 |
| Configuration du préréglage |Configuration de Face Detector | **mode** : FaceRedactorMode.Combined, **blurType** : BlurType.Med, **resolution** : AnalysisResolution.SourceResolution |
| Élément multimédia de sortie |"ignite-redacted.mp4 |Vidéo avec effet de floutage appliqué aux visages |

### <a name="analyze-mode"></a>Mode Analyser

La passe **Analyze** du workflow en deux passes accepte une entrée vidéo et produit un fichier JSON avec une liste des localisations de visage, des ID de visage et des images jpg de chaque visage détecté. 

| Étape | Nom de fichier | Notes |
| --- | --- | --- |
| Élément multimédia d’entrée |"ignite-sample.mp4" |Vidéo au format WMV, MPV ou MP4 |
| Configuration du préréglage |Configuration de Face Detector |**mode** : FaceRedactorMode.Analyze, **resolution** : AnalysisResolution.SourceResolution|
| Élément multimédia de sortie |ignite-sample_annotations.json |Données d’annotation des emplacements de visage au format JSON. Cela peut être modifié par l’utilisateur pour changer les cadres de limitation du flou. Voir l’exemple ci-dessous. |
| Élément multimédia de sortie |foo_thumb%06d.jpg [foo_thumb000001.jpg, foo_thumb000002.jpg] |Une image jpg rognée de chaque visage détecté, où le nombre indique l’ID d’étiquette du visage |

#### <a name="output-example"></a>Exemple de sortie

```json
{
  "version": 1,
  "timescale": 24000,
  "offset": 0,
  "framerate": 23.976,
  "width": 1280,
  "height": 720,
  "fragments": [
    {
      "start": 0,
      "duration": 48048,
      "interval": 1001,
      "events": [
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [
          {
            "index": 13,
            "id": 1138,
            "x": 0.29537,
            "y": -0.18987,
            "width": 0.36239,
            "height": 0.80335
          },
          {
            "index": 13,
            "id": 2028,
            "x": 0.60427,
            "y": 0.16098,
            "width": 0.26958,
            "height": 0.57943
          }
        ],

    ... truncated
```

### <a name="redact-blur-mode"></a>Mode Redact (floutage)

La deuxième passe du flux de travail prend un plus grand nombre d’entrées qui doivent être combinées en un seul élément multimédia.

Cela inclut une liste des ID à flouter, la vidéo d’origine et les annotations JSON. Ce mode utilise les annotations pour appliquer le flou sur la vidéo d’entrée.

La sortie de la passe Analyser n’inclut pas la vidéo d’origine. La vidéo doit être chargée dans l’élément multimédia d’entrée pour la tâche en mode Rédiger et sélectionnée comme fichier principal.

| Étape | Nom de fichier | Notes |
| --- | --- | --- |
| Élément multimédia d’entrée |"ignite-sample.mp4" |Vidéo au format WMV, MPV ou MP4. Même vidéo que celle de l’étape 1. |
| Élément multimédia d’entrée |"ignite-sample_annotations.json" |Fichier de métadonnées d’annotations de la première phase, avec des modifications facultatives si vous souhaitez changer les visages floutés. Vous devez le modifier dans une application externe, en écrivant du code ou via un éditeur de texte. |
| Élément multimédia d’entrée | "ignite-sample_IDList.txt" (facultatif) | Nouvelle liste facultative séparée par des lignes des ID de visage à traiter. Si aucune valeur n’est indiquée, un floutage est appliqué à tous les visages de la source. Vous pouvez utiliser la liste pour ne pas flouter des visages spécifiques. |
| Préréglage de Face Detector  |Configuration du préréglage  | **mode** : FaceRedactorMode.Redact, **blurType** : BlurType.Med |
| Élément multimédia de sortie |"ignite-sample-redacted.mp4" |Vidéo avec flou appliqué en fonction des annotations |

#### <a name="example-output"></a>Exemple de sortie

Il s’agit de la sortie à partir d’une liste d’ID avec un ID sélectionné.

Exemple : foo_IDList.txt

```
1
2
3
```

## <a name="blur-types"></a>Types de flou

En mode **Combined** ou **Redact**, vous avez le choix entre cinq modes de floutage différents via la configuration d’entrée JSON : **Low**, **Med**, **High**, **Box** et **Black**. Par défaut, **Med** (Moyen) est utilisé.

Vous trouverez des exemples de types de flou ci-dessous.


#### <a name="low"></a>Faible

![Exemple illustrant le paramètre de floutage de faible résolution.](./media/media-services-face-redaction/blur-1.png)

#### <a name="med"></a>Med (Moyen)

![Exemple illustrant le paramètre de floutage de résolution moyenne.](./media/media-services-face-redaction/blur-2.png)

#### <a name="high"></a>Élevé

![Exemple illustrant le paramètre de floutage de haute résolution.](./media/media-services-face-redaction/blur-3.png)

#### <a name="box"></a>Box

![Mode Box à utiliser pour déboguer la sortie.](./media/media-services-face-redaction/blur-4.png)

#### <a name="black"></a>Noir

![Le mode Black couvre tous les visages avec des boîtes noires.](./media/media-services-face-redaction/blur-5.png)

## <a name="elements-of-the-output-json-file"></a>Éléments du fichier de sortie JSON

Le processeur multimédia de rédaction permet une détection d’emplacement et un suivi de visage très précis ; il peut détecter jusqu’à 64 visages humains dans une séquence vidéo. Les visages filmés de face donnent les meilleurs résultats ; les visages filmés de côté ou les visages de taille réduite (24 x 24 pixels ou moins) posent plus de problèmes.

[!INCLUDE [media-services-analytics-output-json](../../../includes/media-services-analytics-output-json.md)]

## <a name="net-sample-code"></a>Exemple de code .NET

Le programme suivant montre comment utiliser le mode **Combined**, mode d’édition en une seule passe :

- Créer un élément multimédia et charger un fichier multimédia dans l’élément multimédia.
- Configurez le préréglage de Face Detector qui utilise les paramètres mode et blurType.
- Créez une transformation à l’aide du préréglage de Face Detector
- Téléchargez le fichier vidéo édité de sortie.

## <a name="download-and-configure-the-sample"></a>Télécharger et configurer l’exemple

Clonez un référentiel GitHub qui contient l’exemple .NET sur votre machine à l’aide de la commande suivante :  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet.git
 ```

L’exemple se trouve dans le dossier [FaceRedactor](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoAnalytics/FaceRedactor). Ouvrez [appsettings.json](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/VideoAnalytics/FaceRedactor/appsettings.json) dans votre projet téléchargé. Remplacez les valeurs par les informations d’identification que vous avez obtenues en [accédant aux API](./access-api-howto.md).

**Éventuellement**, copiez le fichier **[sample.env](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/sample.env)** à la racine du dépôt et renseignez les informations nécessaires, puis renommez ce fichier en **.env** (notez le point devant !) afin qu’il puisse être utilisé dans tous les exemples de projets du dépôt.  Cela vous évite d’avoir à fournir un fichier appsettings.json rempli dans chaque exemple, et vous empêche également de modifier des paramètres dans vos propres dépôts clonés GitHub.

#### <a name="examples"></a>Exemples

Ce code montre comment configurer **FaceDetectorPreset** pour un floutage en mode **Combined**.

[!code-csharp[Main](../../../media-services-v3-dotnet/VideoAnalytics/FaceRedactor/Program.cs#FaceDetectorPreset)]

Cet exemple de code montre comment le préréglage est passé en objet Transform durant le processus de création. Une fois la transformation créée, les travaux peuvent lui être envoyés directement.

[!code-csharp[Main](../../../media-services-v3-dotnet/VideoAnalytics/FaceRedactor/Program.cs#FaceDetectorPresetTransform)]

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fournir des commentaires

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

