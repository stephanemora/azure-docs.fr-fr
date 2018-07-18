---
title: Guide pratique pour créer un modèle acoustique avec le service Speech - Microsoft Cognitive Services | Microsoft Docs
description: Découvrez comment créer un modèle acoustique avec le service Speech dans Microsoft Cognitive Services.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: speech-service
ms.topic: tutorial
ms.date: 06/25/2018
ms.author: panosper
ms.openlocfilehash: 3e247b6f81a9306cbad630d42c3a41d8ce3045a5
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2018
ms.locfileid: "37345122"
---
# <a name="tutorial-create-a-custom-acoustic-model"></a>Tutoriel : Créer un modèle acoustique personnalisé

La création d’un modèle acoustique personnalisé est pratique si votre application est conçue pour une utilisation dans un environnement spécifique, par exemple une voiture, avec des appareils d’enregistrement ou des conditions spécifiques, ou par une population d’utilisateurs particulière. Par exemple, vous pouvez avoir à traiter des façons de parler avec un accent, des bruits de fond spécifiques ou l’utilisation d’un microphone spécifique pour l’enregistrement.

Dans cette page, vous découvrez comment :
> [!div class="checklist"]
> * Préparer les données
> * Importer le jeu de données acoustiques
> * Créer le modèle acoustique personnalisé

Si vous n’avez pas de compte Cognitive Services, créez un [compte gratuit](https://azure.microsoft.com/try/cognitive-services) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Vérifiez que votre compte Cognitive Services est connecté à un abonnement en ouvrant la page [Cognitive Services Subscriptions](https://customspeech.ai/Subscriptions) (Abonnements Cognitive Services).

Vous pouvez vous connecter à un abonnement au service Speech créé dans le portail Azure en cliquant sur le bouton **Se connecter à un abonnement existant**.

Pour plus d’informations sur la création d’un abonnement au service Speech dans le portail Azure, consultez la page [Bien démarrer](get-started.md).

## <a name="prepare-the-data"></a>Préparer les données

Pour personnaliser un modèle acoustique pour un domaine particulier, une collection de données vocales est nécessaire. Cela peut aller de quelques énoncés à plusieurs centaines d’heures de parole. Cette collection se compose d’un ensemble de fichiers audio de données vocales et d’un fichier texte de transcriptions pour chaque fichier audio. Les données audio doivent être représentatives du scénario où vous voulez utiliser le module de reconnaissance.

Par exemple :

*   Si vous voulez mieux reconnaître la parole dans un environnement bruyant comme une usine, les fichiers audio doivent être constitués d’enregistrements de personnes parlant dans une usine bruyante.
*   Si vous vous intéressez à l’optimisation des performances pour un seul locuteur, vous pouvez par exemple transcrire tous les Fireside Chats de Franklin D. Roosevelt. Les fichiers audio doivent dans ce cas être constitués de nombreux exemples exclusivement de ce locuteur.

Un jeu de données acoustiques pour personnaliser le modèle acoustique se compose de deux parties : (1) un ensemble de fichiers audio contenant les données vocales et (2) un fichier contenant les transcriptions de tous les fichiers audio.

### <a name="audio-data-recommendations"></a>Recommandations pour les données audio

*   Tous les fichiers audio du jeu de données doivent être stockés au format audio WAV (RIFF).
*   L’audio doit avoir un taux d’échantillonnage de 8 ou 16 kHz, et les valeurs échantillonnées doivent être stockées sous forme d’entiers (courts) de 16 bits PCM non compressés.
*   Seuls les fichiers audio avec un seul canal (mono) sont pris en charge.
*   Les fichiers audio doivent avoir une durée comprise entre 100 ms et 1 minute. Chaque fichier audio doit idéalement commencer et se terminer par un silence d’au moins 100 ms, un silence d’une durée comprise entre 500 ms et 1 seconde étant courant.
*   Si vous avez un bruit de fond dans vos données, il est recommandé d’avoir également quelques exemples avec des segments de silence plus longs, par exemple quelques secondes, dans vos données, avant et/ou après le contenu vocal.
*   Chaque fichier audio doit être constitué d’un seul énoncé, par exemple une seule phrase prononcée, une seule requête ou une seule réplique d’un dialogue.
*   Chaque fichier audio du jeu de données doit avoir un nom de fichier unique et l’extension « wav ».
*   L’ensemble de fichiers audio doit être placé dans un même dossier sans sous-répertoires, et tous les fichiers doivent se trouver dans un même fichier d’archive ZIP.

> [!NOTE]
> Les importations de données via le portail web sont actuellement limitées à 2 Go : il s’agit donc de la taille maximale d’un jeu de données acoustiques. Ceci correspond à environ 17 heures d’audio enregistrées à 16 kHz, ou à 34 heures d’audio enregistrées à 8 kHz. Les principales spécifications pour les données audio sont résumées dans le tableau suivant.
>

| Propriété | Valeur |
|---------- |----------|
| Format de fichier | RIFF (WAV) |
| Taux d’échantillonnage | 8 000 Hz ou 16 000 Hz |
| Canaux | 1 (mono) |
| Format d’échantillonnage | PCM, entiers sur 16 bits |
| Durée du fichier | 0,1 seconde < durée < 60 secondes |
| Silence de début/fin | > 0,1 seconde |
| Format d’archive | Zip |
| Taille d’archive maximale | 2 Go |

## <a name="language-support"></a>Support multilingue

Les langues prises en charge pour les modèles de langage de **reconnaissance vocale** personnalisés sont répertoriées ci-après.

Cliquez pour obtenir une liste complète des [langues prises en charge](supported-languages.md)

### <a name="transcriptions-for-the-audio-dataset"></a>Transcriptions pour le jeu de données audio

Les transcriptions de tous les fichiers WAV doivent se trouver dans un seul fichier en texte brut. Chaque ligne du fichier de transcription doit avoir le nom d’un des fichiers audio, suivi de la transcription correspondante. Le nom de fichier et la transcription doivent être séparés par une tabulation (\t).

  Par exemple :
```
  speech01.wav  speech recognition is awesome
  speech02.wav  the quick brown fox jumped all over the place
  speech03.wav  the lazy dog was not amused
```
> [!NOTE]
> La transcription doit être encodée au format UTF-8 BOM

Les transcriptions doivent être en texte normalisé pour pouvoir être traitées par le système. Une normalisation importante doit toutefois être effectuée par l’utilisateur _avant_ de charger les données dans Custom Speech Service. Consultez la section sur [les instructions de transcription](prepare-transcription.md) pour la langue appropriée lors de la préparation de vos transcriptions.

Les étapes suivantes sont effectuées dans le [portail du service Speech](https://customspeech.ai).

## <a name="import-the-acoustic-data-set"></a>Importer le jeu de données acoustiques

Une fois que les fichiers audio et les transcriptions ont été préparés, ils sont prêts à être importé dans le portail web du service.

Pour cela, vérifiez d’abord que vous êtes connecté au [portail du service Speech](https://customspeech.ai). Cliquez ensuite sur le menu déroulant « Custom Speech » dans le ruban supérieur, puis sélectionnez « Adaptation Data » (Données d’adaptation). S’il s’agit de votre premier chargement de données vers Custom Speech Service, vous verrez un tableau vide appelé « Datasets » (Jeux de données). 

Cliquez sur le bouton « Import » (Importer) dans la ligne « Acoustic Datasets » (Jeux de données acoustiques) : le site affiche une page pour le chargement d’un nouveau jeu de données.

![Essayer](media/stt/speech-acoustic-datasets-import.png)

Entrez un _Nom_ et une _Description_ dans les zones de texte appropriées. Des descriptions claires sont pratiques pour effectuer le suivi des différents jeux de données que vous chargez. Ensuite, cliquez sur « Choose File » (Choisir un fichier) pour le « Transcription File » (Fichier de transcription) et les « WAV files » (Fichiers WAV ), et sélectionnez votre fichier de transcription en texte brut et le fichier d’archive zip des fichiers WAV, respectivement. Quand la préparation est terminée, cliquez sur « Import » (Importer) pour charger vos données. Vos données sont alors chargées. Pour les jeux de données plus grands, cette opération peut prendre plusieurs minutes.

Quand le chargement est terminé, vous revenez au tableau « Acoustic Datasets » (Jeux de données acoustiques) et vous voyez une entrée qui correspond à votre jeu de données acoustiques. Notez qu’un ID unique (GUID) lui a été affecté. Les données contiennent également un état qui reflète leur état actuel. Leur état est « NotStarted » (Non Démarré) tant qu’elles sont en file d’attente avant traitement, « Running » (En cours d’exécution) pendant la phase de validation et « Complete » (Terminé) quand les données sont prêtes à être utilisées.

La validation des données inclut une série de vérifications sur les fichiers audio quant au format, à la longueur et au taux d’échantillonnage des fichiers, et sur les fichiers de transcription pour vérifier le format et effectuer une normalisation du texte.

Quand l’état est « Succeeded » (Réussi), vous pouvez cliquer sur « Details » (Détails) pour voir le rapport de vérification des données acoustiques. Le nombre d’énoncés pour lesquels la vérification a réussi ou échoué est affiché, ainsi que des détails sur les énoncés en échec. Dans l’exemple ci-dessous, la vérification a échoué pour deux fichiers WAV en raison d’un format audio incorrect (dans ce jeu de données, un avait un taux d’échantillonnage incorrect et un avait un format de fichier incorrect).

![Essayer](media/stt/speech-acoustic-datasets-report.png)

À un moment donné, si vous voulez changer le nom ou la description du jeu de données, vous pouvez cliquer sur le lien « Edit » (Modifier) et changer ces entrées. Vous ne pouvez pas modifier les fichiers audio ou les transcriptions.

## <a name="create-a-custom-acoustic-model"></a>Créer un modèle acoustique personnalisé

Une fois que l’état de votre jeu de données acoustiques est « Complete » (Terminé), il peut être utilisé pour créer un modèle acoustique personnalisé. Pour cela, cliquez sur « Acoustic Models » (Modèles acoustiques) dans le menu déroulant « Custom Speech ». Vous voyez un tableau nommé « Your models » (Vos modèles), qui répertorie tous vos modèles acoustiques personnalisés. Ce tableau est vide s’il s’agit de votre première utilisation. Les paramètres régionaux actuels sont indiqués dans le titre du tableau. Actuellement, les modèles acoustiques peuvent être créés seulement pour l’anglais des États-Unis.

Pour créer un modèle, cliquez sur « Create New » (Créer nouveau) sous le titre du tableau. Comme auparavant, entrez un nom et une description pour vous aider à identifier ce modèle. Par exemple, le champ « Description » peut être utilisé pour indiquer le modèle de départ et le jeu de données acoustiques qui ont été utilisés pour créer le modèle. Sélectionnez ensuite un « Base Acoustic Model » (Modèle acoustique de base) dans le menu déroulant. Le modèle de base est le modèle qui est le point de départ de votre personnalisation. Vous pouvez choisir entre deux modèles acoustiques de base. Les _modèles acoustiques Microsoft Search and Dictation (Recherche et Dictée)_ est approprié pour la voix dans une application, comme les commandes, les requêtes de recherche ou la dictée. Le _modèle Microsoft Conversational (Oral)_ est approprié pour la reconnaissance vocale de paroles prononcées dans un style oral. Ce type de discours s’adresse généralement à une autre personne, et peut se tenir dans des centres d’appel ou des réunions. Le temps d’attente des résultats partiels dans les modèles Oral est supérieur à celui des modèles Search and Dictation (Recherche et Dictée).

> [!NOTE]
> Nous déployons actuellement notre nouveau modèle Universal (Universel) qui vise à résoudre tous les scénarios. Les modèles mentionnés ci-dessus resteront disponibles publiquement.

Ensuite, sélectionnez les données acoustiques que vous voulez utiliser pour effectuer la personnalisation avec le menu déroulant.

![Essayer](media/stt/speech-acoustic-models-create2.png)

Vous pouvez choisir d’effectuer des tests de précision de votre nouveau modèle une fois le traitement terminé. Ceci effectue une évaluation de la conversion parole-texte sur un jeu de données acoustiques spécifié avec le modèle acoustique personnalisé et produit un rapport des résultats. Pour effectuer ce test, cochez la case « Accuracy Testing » (Tests de précision). Sélectionnez ensuite un modèle de langage dans le menu déroulant. Si vous n’avez pas créé de modèle de langage personnalisé, seuls les modèles de langage de base se trouvent dans la liste déroulante. Consultez la [description](how-to-customize-language-model.md) des modèles de langage de base dans le guide, puis sélectionnez le plus approprié.

Enfin, sélectionnez le jeu de données acoustiques que vous voulez utiliser pour évaluer le modèle personnalisé. Si vous effectuez des tests de précision, il est important de sélectionner des données acoustiques qui sont différentes de celles utilisées pour la création du modèle, de façon à vous faire une idée réaliste des performances du modèle. Des tests de précision sur les données d’apprentissage ne vous permettent pas d’évaluer comment le modèle adapté fonctionne dans des conditions réelles. Le résultat sera trop optimiste. Notez également que les tests de précision sont limités à 1 000 énoncés. Si le jeu de données acoustiques pour le test est plus grand, seuls les 1 000 premiers énoncés sont évalués.

Quand vous êtes prêt à commencer le processus de personnalisation, cliquez sur « Create » (Créer).

Vous voyez maintenant une nouvelle entrée dans le tableau des modèles acoustiques, qui correspond à ce nouveau modèle. L’état du processus est affiché dans le tableau. Les états possibles sont « Waiting » (En attente), « Processing » (En cours de traitement) et « Complete » (Terminé).

![Essayer](media/stt/speech-acoustic-models-creating.png)

## <a name="next-steps"></a>Étapes suivantes

- [Obtenir votre abonnement d’essai gratuit du service Speech](https://azure.microsoft.com/try/cognitive-services/)
- [Reconnaissance vocale dans C#](quickstart-csharp-windows.md)
- [Exemples de données Git](https://github.com/Microsoft/Cognitive-Custom-Speech-Service)
