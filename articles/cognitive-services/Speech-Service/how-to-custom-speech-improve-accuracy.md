---
title: Améliorer un modèle pour Custom Speech - Service Speech
titleSuffix: Azure Cognitive Services
description: Des types particuliers de transcriptions étiquetées à la main et de texte associé peuvent améliorer la précision de la reconnaissance d’un modèle de reconnaissance vocale en fonction du scénario d’élocution.
services: cognitive-services
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: v-demjoh
ms.openlocfilehash: bb904482f3cb5900b724803816269f1b10ab3720
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83727865"
---
# <a name="improve-custom-speech-accuracy"></a>Améliorer la justesse de Custom Speech

Dans cet article, vous allez apprendre à améliorer la qualité de votre modèle personnalisé en ajoutant de l’audio, des transcriptions étiquetées à la main et le texte associé.

## <a name="accuracy-in-different-scenarios"></a>Précision dans différents scénarios

Les scénarios de reconnaissance vocale varient en fonction de la qualité et de la langue de l’audio (vocabulaire et style d’élocution). Le tableau suivant examine quatre scénarios courants :

| Scénario | Qualité audio | Vocabulaire | Style d’élocution |
|----------|---------------|------------|----------------|
| Centre d’appels | Faible, 8 kHz, possibilité d’avoir 2 personnes sur 1 canal audio, peut être compressée | Étroit, propre au domaine et aux produits | Conversationnel, vaguement structuré |
| Assistant vocal (tel que Cortana ou une fenêtre de drive) | Élevée, 16 kHz | Lourd au niveau des entités (titres de chanson, produits, emplacements) | Mots et phrases clairement énoncés |
| Dictée (message instantané, notes, recherche) | Élevée, 16 kHz | Différentes possibilités | Prise de note |
| Sous-titrage vidéo | Variée (utilisation variée du microphone, ajout de musique, etc.) | Varié (réunions, discours, paroles de chanson, etc.) | Texte lu, préparé ou faiblement structuré |

Différents scénarios produisent des résultats de qualité différents. Le tableau suivant examine le [taux d’erreurs sur les mots](how-to-custom-speech-evaluate-data.md) (WER, Word Error rate) du contenu de ces quatre scénarios. Le tableau indique les types d’erreur les plus courants dans chaque scénario.

| Scénario | Qualité de la reconnaissance vocale | Nombre d’erreurs d’insertion | Nombre d’erreurs de suppression | Nombre d’erreurs de substitution |
|----------|----------------------------|------------------|-----------------|---------------------|
| Centre d’appels | Moyen (< 30 % WER) | Faible, sauf quand d’autres personnes parlent en arrière-plan | Peut être élevé Les centres d’appels peuvent être bruyants et les opérateurs qui parlent en même temps peuvent être source de confusion pour le modèle | Moyenne. Les noms de produits et de personnes peuvent entraîner ces erreurs |
| Assistant vocal | Élevé (peut être < 10 % WER) | Faible | Faible | Moyen, en raison de titres de chanson, de noms de produits ou de localisations |
| Dictation | Élevé (peut être < 10 % WER) | Faible | Faible | Élevé |
| Sous-titrage vidéo | Dépend du type de vidéo (peut être < 50 % WER) | Faible | Peut être élevé en raison de la musique, des bruits et de la qualité du microphone | Le jargon peut entraîner ces erreurs |

La détermination des composants du WER (nombre d’erreurs d’insertion, de suppression et de substitution) permet de décider du type de données à ajouter pour améliorer le modèle. Utilisez le [portail Custom Speech](https://speech.microsoft.com/customspeech) pour voir la qualité d’un modèle de référence. Le portail signale les taux d’erreur d’insertion, de substitution et de suppression qui sont combinés dans le taux de qualité WER.

## <a name="improve-model-recognition"></a>Améliorer la reconnaissance du modèle

Vous pouvez réduire les erreurs de reconnaissance en ajoutant des données d’entraînement dans le portail [Custom Speech](https://speech.microsoft.com/customspeech). 

Prévoyez de maintenir votre modèle personnalisé en ajoutant régulièrement des éléments sources. Votre modèle personnalisé a besoin d’un entraînement supplémentaire pour rester informé des modifications apportées à vos entités. Par exemple, vous devrez peut-être mettre à jour les noms de produits, noms de chansons ou nouveaux emplacements de service.

Les sections suivantes décrivent comment chaque type de données d’entraînement supplémentaires peut réduire les erreurs.

### <a name="add-related-text-sentences"></a>Ajouter des phrases de texte associées

L’ajout de phrases de texte associées permet principalement de réduire les erreurs de substitution liées à la reconnaissance erronée des mots courants et des mots spécifiques à un domaine en les présentant en contexte. Les mots spécifiques à un domaine peuvent être des mots rares ou inventés, mais leur prononciation doit être facile à reconnaître.

> [!NOTE]
> Évitez les phrases de texte associées qui incluent du bruit, comme des caractères ou des mots non reconnaissables.

### <a name="add-audio-with-human-labeled-transcripts"></a>Ajouter de l’audio avec des transcriptions étiquetées à la main

L’audio avec des transcriptions étiquetées à la main offre les plus fortes améliorations en termes de précision si l’audio provient du cas d’usage cible. Les exemples doivent couvrir l’intégralité des messages vocaux. Par exemple, la plupart des appels adressés à un centre d’appels pendant les mois d’été concernent les maillots de bain et les lunettes de soleil. Vérifiez que votre exemple comprend l’étendue complète des messages vocaux que vous souhaitez détecter.

Tenez compte des détails suivants :

* Custom Speech peut uniquement capturer le contexte des mots pour réduire les erreurs de substitution, pas les erreurs d’insertion ou de suppression.
* Évitez les exemples comprenant des erreurs de transcription, mais incluez une qualité audio diversifiée.
* Évitez les phrases qui ne sont pas liées au domaine de votre problème. Les phrases non liées peuvent endommager votre modèle.
* En cas de variation de la qualité des transcriptions, vous pouvez dupliquer des phrases exceptionnellement bonnes (par exemple, d’excellentes transcriptions qui incluent des expressions clés) pour augmenter leur poids.

### <a name="add-new-words-with-pronunciation"></a>Ajouter de nouveaux mots avec la prononciation

Les mots inventés ou hautement spécialisés peuvent avoir des prononciations uniques. Ces mots peuvent être reconnus si le mot peut être décomposé en mots plus petits pour le prononcer. Par exemple, pour reconnaître **Xbox**, prononcez ce mot **X box**. Cette approche n’augmente pas la précision globale, mais elle peut accroître la reconnaissance de ces mots clés.

> [!NOTE]
> Cette technique n’est disponible que pour certaines langues à l’heure actuelle. Pour plus d’informations, consultez la personnalisation de la prononciation dans le [tableau de reconnaissance vocale](language-support.md).

## <a name="sources-by-scenario"></a>Sources par scénario

Le tableau suivant présente les scénarios de reconnaissance vocale et liste les documents sources à prendre en compte dans les trois catégories de contenu d’entraînement ci-dessus.

| Scénario | Phrases de texte associées | Transcriptions audio + étiquetées à la main | Nouveaux mots avec prononciation |
|----------|------------------------|------------------------------|------------------------------|
| Centre d’appels             | documents marketing, site web, évaluations de produits relatives à l’activité du centre d’appels | appels du centre d’appels transcrits à la main | termes qui ont des prononciations ambiguës (voir Xbox ci-dessus) |
| Assistant vocal         | lister les phrases à l’aide de toutes les combinaisons de commandes et d’entités | enregistrer les commandes vocales sur l’appareil et transcrire en texte | noms (films, chansons, produits) qui ont des prononciations uniques |
| Dictation               | entrée écrite, comme les messages instantanés ou les e-mails | semblable à ci-dessus | semblable à ci-dessus |
| Sous-titrage vidéo | scripts de programmes télévisés, films, contenu marketing, résumés vidéo | transcriptions de vidéos exactes | semblable à ci-dessus |

## <a name="next-steps"></a>Étapes suivantes

- [Entraîner un modèle](how-to-custom-speech-train-model.md)

## <a name="additional-resources"></a>Ressources supplémentaires

- [Préparer et tester les données](how-to-custom-speech-test-data.md)
- [Inspecter les données](how-to-custom-speech-inspect-data.md)