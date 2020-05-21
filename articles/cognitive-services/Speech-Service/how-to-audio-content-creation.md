---
title: Création de contenu audio - Service Speech
titleSuffix: Azure Cognitive Services
description: Création de contenu audio est un outil en ligne qui vous permet de personnaliser et d’affiner le résultat de la conversion de texte par synthèse vocale de Microsoft pour vos applications et vos produits.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: trbye
ms.openlocfilehash: 083580435c467a7d4b6a4cede0a821a2c271962f
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83589650"
---
# <a name="improve-synthesis-with-the-audio-content-creation-tool"></a>Améliorer la synthèse avec l’outil de création de contenu audio

[Création de contenu audio](https://aka.ms/audiocontentcreation) est un outil en ligne qui vous permet de personnaliser et d’affiner le résultat de la conversion de texte par synthèse vocale de Microsoft pour vos applications et vos produits. Vous pouvez utiliser cet outil pour affiner les voix publiques et personnalisées afin d’obtenir des expressions naturelles plus précises et gérer votre sortie dans le Cloud.

L’outil Création de contenu audio est basé sur le [SSML (Speech Synthesis Markup Language)](speech-synthesis-markup.md). Pour simplifier la personnalisation et le paramétrage, Création de contenu audio vous permet d’inspecter visuellement vos sorties de conversion de texte par synthèse vocale en temps réel.

## <a name="how-does-it-work"></a>Comment cela fonctionne-t-il ?

Ce diagramme vous montre les étapes nécessaires pour régler les sorties de la conversion de texte par synthèse vocale. Utilisez les liens ci-dessous pour en savoir plus sur chaque étape.

![](media/audio-content-creation/audio-content-creation-diagram.jpg)

1. Pour commencer, [configurez votre compte Azure et votre ressource Speech](#set-up-your-azure-account-and-speech-resource).
2. [Créez un fichier de réglage audio](#create-an-audio-tuning-file) en utilisant des scripts en texte brut ou SSML.
3. Choisissez la voix et la langue du contenu de votre script. Création de contenu audio comprend toutes les [voix de synthèse vocale de Microsoft](language-support.md#text-to-speech). Vous pouvez utiliser une voix standard, neuronale ou votre propre voix personnalisée.
   >[!NOTE]
   > L’accès contrôlé est disponible pour les voix neuronales personnalisées, ce qui vous permet de créer des voix haute définition similaires à une voix naturelle. Pour plus d’informations, consultez [Processus de vérification](https://aka.ms/ignite2019/speech/ethics).

4. Examinez la sortie de synthèse par défaut. Améliorez ensuite la sortie en ajustant la prononciation, les pauses, le ton, le débit, l’intonation, le style vocal, etc. Pour obtenir la liste complète des options, consultez [Langage de balisage de synthèse vocale](speech-synthesis-markup.md). Voici une [vidéo](https://youtu.be/mUvf2NbfuYU) illustrant comment ajuster la sortie vocale avec la Création de contenu audio. 
5. Enregistrez et [exportez vos audio réglés](#export-tuned-audio). Lorsque vous enregistrez la piste de paramétrage dans le système, vous pouvez continuer à travailler et itérer sur la sortie. Lorsque vous êtes satisfait de la sortie, vous pouvez créer une tâche de création audio avec la fonctionnalité d’exportation. Vous pouvez observer l’état de la tâche d’exportation et télécharger la sortie à utiliser avec vos applications et produits.

## <a name="set-up-your-azure-account-and-speech-resource"></a>Configurer votre compte Azure et votre ressource Speech

1. Pour utiliser la Création de contenu audio, vous devez avoir un compte Azure. Vous pouvez créer un compte Azure en utilisant votre compte Microsoft. Suivez ces instructions pour [configurer un compte Azure](get-started.md#new-resource). 
2. [Créez une ressource Speech](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-the-resource) dans votre compte Azure. Assurez-vous que votre niveau tarifaire est défini sur **S0**. Si vous utilisez l’une des voix neuronales, assurez-vous de créer votre ressource dans une [région prise en charge](regions.md#standard-and-neural-voices).
2. Lorsque le compte Azure et la ressource sont prêts, vous pouvez utiliser les services vocaux et accéder à la [Création de contenu audio](https://aka.ms/audiocontentcreation).
3. Sélectionnez la ressource Speech sur laquelle vous voulez travailler. Vous pouvez également créer une ressource Speech ici. 
4. Vous pouvez modifier votre ressource Speech à tout moment avec l’option **Paramètres**, située dans le haut de la section.

## <a name="create-an-audio-tuning-file"></a>Créer un fichier de réglage audio

Il existe deux façons d’intégrer votre contenu dans l’outil Création de contenu audio.

**Option 1 :**

1. Cliquez sur **Nouveau fichier** pour créer un nouveau fichier de réglage audio.
2. Tapez ou collez votre contenu dans la fenêtre d’édition. Chaque fichier ne doit pas excéder 20 000 caractères. Si votre script dépasse cette limite, vous pouvez utiliser l’option 2 pour fractionner automatiquement votre contenu en plusieurs fichiers. 
3. N’oubliez pas d’enregistrer.

**Option 2 :**

1. Cliquez sur **Charger** pour importer un ou plusieurs fichiers texte. Le texte brut et le SSML sont pris en charge.
2. Si votre fichier de script comporte plus de 20 000 caractères, divisez le fichier par paragraphes, caractères ou expressions régulières. 
3. Lorsque vous téléchargez vos fichiers texte, assurez-vous que le fichier répond à ces exigences.

   | Propriété | Valeur/remarques |
   |----------|---------------|
   | Format de fichier | Texte brut (.txt)<br/> Texte SSML (.txt)<br/> Les fichiers zip ne sont pas pris en charge. |
   | Format d’encodage | UTF-8 |
   | Nom de fichier | Chaque fichier doit avoir un nom unique. Les doublons ne sont pas pris en charge. |
   | Longueur du texte | Les fichiers texte ne doivent pas dépasser 20 000 caractères. |
   | Restrictions SSML | Chaque fichier SSML ne peut contenir qu’un seul élément SSML. |

### <a name="plain-text-example"></a>Exemple de texte brut

```txt
Welcome to use Audio Content Creation to customize audio output for your products.
```

### <a name="ssml-text-example"></a>Exemple de texte SSML

```xml
<speak xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="http://www.w3.org/2001/mstts" version="1.0" xml:lang="en-US">
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, AriaNeural)">
    Welcome to use Audio Content Creation <break time="10ms" />to customize audio output for your products.
    </voice>
</speak>
```

## <a name="export-tuned-audio"></a>Exporter un audio réglé

Une fois que vous avez vérifié la sortie audio et que vous êtes satisfait de votre paramétrage et de votre réglage, vous pouvez exporter l’audio.

1. Cliquez sur **Exporter** pour créer une tâche de création d’audio. Il est recommandé d’**exporter vers une bibliothèque audio**, car celle-ci prend en charge la longue sortie audio et l’expérience de sortie audio dans son intégralité. Vous pouvez également télécharger l’audio directement vers votre disque local, mais seules les 10 première minutes sont disponibles. 
2. Choisissez le format de sortie de votre audio réglé. Vous trouverez ci-dessous une liste des formats et des taux d’échantillonnage pris en charge.
3. Vous pouvez afficher l’état de la tâche dans l’onglet **Exporter une tâche**. Si la tâche échoue, consultez la page d’informations détaillées pour obtenir un rapport complet.
4. Une fois la tâche terminée, votre audio est disponible en téléchargement dans l’onglet **Bibliothèque audio**.
5. Cliquez sur **Télécharger**. Vous êtes maintenant prêt à utiliser votre audio réglé personnalisé dans vos applications ou vos produits.

### <a name="supported-audio-formats"></a>Formats audio pris en charge

| Format | Taux d’échantillonnage 16 kHz | Taux d’échantillonnage 24 kHz |
|--------|--------------------|--------------------|
| wav | riff-16 khz-16 bits-mono-pcm | riff-24 khz-16 bits-mono-pcm |
| mp3 | audio-16 khz-128 kbitrate-mono-mp3 | audio-24 khz-160 kbitrate-mono-mp3 |

## <a name="see-also"></a>Voir aussi

* [API Audio long](https://aka.ms/long-audio-api)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Speech Studio](https://speech.microsoft.com)
