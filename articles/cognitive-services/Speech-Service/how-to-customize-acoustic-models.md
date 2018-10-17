---
title: Créer un modèle acoustique avec Speech Service - Azure Cognitive Services
description: Découvrez comment créer un modèle acoustique avec le service Speech dans Azure Cognitive Services.
services: cognitive-services
author: PanosPeriorellis
ms.service: cognitive-services
ms.component: speech-service
ms.topic: tutorial
ms.date: 06/25/2018
ms.author: panosper
ms.openlocfilehash: 39e591f6154573bb25fccc423ff63a82f282beaf
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/02/2018
ms.locfileid: "48017349"
---
# <a name="tutorial-create-a-custom-acoustic-model"></a>Tutoriel : Créer un modèle acoustique personnalisé

La création d’un modèle acoustique personnalisé est pratique si votre application est conçue pour une utilisation dans un environnement spécifique, par exemple une voiture, avec des appareils d’enregistrement ou des conditions spécifiques, ou par une population d’utilisateurs particulière. Par exemple, vous pouvez avoir à traiter des façons de parler avec un accent, des bruits de fond spécifiques ou l’utilisation d’un microphone spécifique pour l’enregistrement.

Dans cet article, vous apprendrez comment :
> [!div class="checklist"]
> * Préparer les données
> * Importer le jeu de données acoustiques
> * Créer le modèle acoustique personnalisé

Si vous n’avez pas de compte Azure Cognitive Services, créez un [compte gratuit](https://azure.microsoft.com/try/cognitive-services) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Vérifiez que votre compte Cognitive Services est connecté à un abonnement en ouvrant la page [Cognitive Services Subscriptions](https://cris.ai/Subscriptions) (Abonnements Cognitive Services).

Vous pouvez vous connecter à un abonnement à Speech Service créé dans le portail Azure en sélectionnant **Se connecter à un abonnement existant**.

Pour plus d’informations sur la création d’un abonnement à Speech Services dans le portail Azure, consultez la page [Essayez le service Speech gratuitement](get-started.md).

## <a name="prepare-the-data"></a>Préparer les données

Pour personnaliser un modèle acoustique pour un domaine particulier, une collection de données vocales est nécessaire. Cela peut aller de quelques énoncés à plusieurs centaines d’heures de parole. La collection se compose d’un ensemble de fichiers audio de données vocales et d’un fichier texte de transcriptions pour chaque fichier audio. Les données audio doivent être représentatives du scénario où vous voulez utiliser le module de reconnaissance.

Par exemple : 

* Si vous voulez mieux reconnaître la parole dans un environnement bruyant comme une usine, les fichiers audio doivent être constitués d’enregistrements de personnes parlant dans une usine bruyante.
* Si vous vous intéressez à l’optimisation des performances pour un seul locuteur (vous voulez par exemple transcrire tous les Fireside Chats de Franklin D. Roosevelt), les fichiers audio doivent être constitués de nombreux exemples exclusivement de ce locuteur.

Un jeu de données acoustiques pour personnaliser le modèle acoustique se compose de deux parties : (1) un ensemble de fichiers audio contenant les données vocales et (2) un fichier contenant les transcriptions de tous les fichiers audio.

### <a name="audio-data-recommendations"></a>Recommandations pour les données audio

* Tous les fichiers audio du jeu de données doivent être stockés au format audio WAV (RIFF).
* L’audio doit avoir un taux d’échantillonnage de 8 ou 16 kilohertz (kHz), et les valeurs échantillonnées doivent être stockées sous forme d’entiers (courts) de 16 bits de modulation d’impulsion codée (PCM) non compressés.
* Seuls les fichiers audio avec un seul canal (mono) sont pris en charge.
* La durée des fichiers audio doit être comprise entre 100 ms et 1 minute. Chaque fichier audio doit idéalement commencer et se terminer par un silence d’au moins 100 ms, un silence d’une durée comprise entre 500 ms et 1 seconde étant courant.
* Si vous avez un bruit de fond dans vos données, nous vous recommandons d’avoir également dans vos données quelques exemples avec des segments de silence plus longs, par exemple quelques secondes, avant et/ou après le contenu vocal.
* Chaque fichier audio doit être constitué d’un seul énoncé, par exemple une seule phrase prononcée, une seule requête ou une seule réplique d’un dialogue.
* Chaque fichier audio du jeu de données doit avoir un nom de fichier unique et l’extension .wav.
* L’ensemble de fichiers audio doit être placé dans un même dossier sans sous-répertoires, et tous les fichiers doivent se trouver dans un même fichier d’archive .zip.

> [!NOTE]
> Les importations de données via le portail web sont actuellement limitées à 2 Go : il s’agit donc de la taille maximale d’un jeu de données acoustiques. Ceci correspond à environ 17 heures d’audio enregistré à 16 kHz, ou à 34 heures d’audio enregistré à 8 kHz. Les principales spécifications pour les données audio sont résumées dans le tableau suivant :
>

| Propriété | Valeur |
|---------- |----------|
| Format de fichier | RIFF (WAV) |
| Taux d’échantillonnage | 8 000 Hertz (Hz) ou 16 000 Hz |
| Canaux | 1 (mono) |
| Format d’échantillonnage | PCM, entiers sur 16 bits |
| Durée du fichier | 0,1 seconde < durée < 60 secondes |
| Silence de début/fin | > 0,1 seconde |
| Format d’archive | .zip |
| Taille d’archive maximale | 2 Go |

> [!NOTE]
> Les noms de fichiers doivent utiliser uniquement des caractères latins et suivre le format « nomdefichier.extension ».

## <a name="language-support"></a>Support multilingue

Pour obtenir la liste complète des langues prises en charge pour les modèles linguistiques personnalisés de **reconnaissance vocale**, consultez [Prise en charge de la langue pour le service de reconnaissance vocale](language-support.md#speech-to-text).

### <a name="transcriptions-for-the-audio-dataset"></a>Transcriptions pour le jeu de données audio

Les transcriptions de tous les fichiers WAV doivent se trouver dans un seul fichier en texte brut. Chaque ligne du fichier de transcription doit contenir le nom d’un des fichiers audio, suivi de la transcription correspondante. Le nom de fichier et la transcription doivent être séparés par une tabulation (\t).

  Par exemple : 
```
  speech01.wav  speech recognition is awesome
  speech02.wav  the quick brown fox jumped all over the place
  speech03.wav  the lazy dog was not amused
```
> [!NOTE]
> La transcription doit être encodée au format UTF-8 marque d'ordre d'octet (BOM).

Les transcriptions doivent être en texte normalisé pour pouvoir être traitées par le système. Une normalisation importante doit toutefois être effectuée par l’utilisateur _avant_ de charger les données dans Custom Speech Service. Pour la langue appropriée à utiliser lorsque vous préparez vos transcriptions, consultez [Instructions concernant la transcription pour l’utilisation du service Speech](prepare-transcription.md).

Procéder aux étapes décrites dans les sections suivantes à l’aide du [portail Speech Service](https://cris.ai).

## <a name="import-the-acoustic-dataset"></a>Importer le jeu de données acoustiques

Une fois que les fichiers audio et les transcriptions ont été préparés, ils sont prêts à être importés dans le portail web du service.

Pour les importer, assurez-vous tout d’abord que vous êtes connecté au [portail Speech Service](https://cris.ai). Ensuite, dans la liste déroulante **Custom Speech** (discours personnalisé) du ruban, sélectionnez **Adaptation Data** (données d’adaptation). S’il s’agit de votre premier chargement de données vers Custom Speech Service, vous verrez un tableau vide appelé **Datasets** (Jeux de données). 

Cliquez sur le bouton **Import** (Importer) dans la ligne **Acoustic Datasets** (Jeux de données acoustiques) : le site affiche une page pour le chargement d’un nouveau jeu de données.

![Page Importer des données acoustiques](media/stt/speech-acoustic-datasets-import.png)

Dans les champs **Name** (Nom) et **Description**, entrez les informations appropriées. Des descriptions claires vous aident à effectuer le suivi des différents jeux de données que vous chargez. 

Dans les cases **Transcriptions files (.txt)** (Fichiers transcriptions) et **Audio Files (.zip)** (fichiers audio), sélectionnez **Browse** (Parcourir), puis sélectionnez votre fichier de transcription en texte brut et l’archive zip des fichiers WAV. Quand la préparation est terminée, cliquez sur **Import** (Importer) pour charger vos données. Vos données sont alors chargées. Pour les jeux de données volumineux, le processus d’importation peut prendre plusieurs minutes.

Lorsque le chargement est terminé, revenez au tableau **Acoustic Datasets** (jeux de données acoustiques). Une entrée s’affiche, qui correspond à votre jeu de données acoustiques. Notez qu’un ID unique (GUID) lui a été affecté. Les données sont à l’état  : *NotStarted* (Non Démarré) tant qu’elles sont en file d’attente avant traitement, *Running* (En cours d’exécution) pendant la phase de validation et *Complete* (Terminé) quand les données sont prêtes à être utilisées.

La validation des données inclut une série de vérifications sur les fichiers audio quant au format, à la longueur et au taux d’échantillonnage des fichiers, et sur les fichiers de transcription pour vérifier le format et effectuer une normalisation du texte.

Quand l’état est *Succeeded* (Réussi), vous pouvez cliquer sur **Details** (Détails) pour voir le rapport de vérification des données acoustiques. Le nombre d’énoncés dont la vérification a réussi ou échoué s’affiche, ainsi que des détails sur les énoncés ayant échoué. Dans l’exemple de l’image suivante, la vérification de deux fichiers WAV a échoué en raison d’un format audio incorrect. Dans ce jeu de données, un fichier montre un taux d’échantillonnage incorrect, et l’autre présente un format de fichier incorrect.

![Page Détails des données d’adaptation](media/stt/speech-acoustic-datasets-report.png)

Si vous voulez changer le nom ou la description du jeu de données, vous pouvez cliquer sur le lien **Edit** (Modifier) et changer ces entrées. Vous ne pouvez pas modifier les entrées des fichiers audio ou des transcriptions.

## <a name="create-a-custom-acoustic-model"></a>Créer un modèle acoustique personnalisé

Une fois que l’état de votre jeu de données acoustiques est *Complete* (Terminé), il peut être utilisé pour créer un modèle acoustique personnalisé. Pour cela, cliquez sur **Acoustic Models** (Modèles acoustiques) dans le menu déroulant **Custom Speech**. Une table intitulée **Your models** (vos modèles) répertorie tous vos modèles acoustiques personnalisés. Ce tableau est vide s’il s’agit de votre première utilisation. Les paramètres régionaux actuels sont indiqués dans le titre du tableau. Actuellement, vous pouvez créer des modèles acoustiques uniquement pour l’anglais des États-Unis.

Pour créer un modèle, cliquez sur **Create New** (Créer nouveau) sous le titre du tableau. Comme auparavant, entrez un nom et une description pour vous aider à identifier ce modèle. Par exemple, le champ **Description** peut être utilisé pour indiquer le modèle de départ et le jeu de données acoustiques qui ont été utilisés pour créer le modèle. 

Ensuite, dans la liste déroulante **Base Acoustic Model** (modèle acoustique de base), sélectionnez un modèle de base. Le modèle de base est le point de départ de votre personnalisation. Vous pouvez choisir entre deux modèles acoustiques de base :
* Le modèle **Microsoft Search and Dictation AM** (Recherche et dictée) est approprié pour la reconnaissance vocale qui est destinée à une application, comme les commandes, les requêtes de recherche ou la dictée. 
* Le **modèle Microsoft Conversational** (Oral) est approprié pour la reconnaissance vocale de paroles prononcées dans un style oral. Ce type de discours s’adresse généralement à une autre personne, et peut se tenir dans des centres d’appel ou des réunions. 

Le temps d’attente des résultats partiels dans les modèles Oral est supérieur à celui des modèles Search and Dictation (Recherche et Dictée).

> [!NOTE]
> Nous déployons actuellement notre nouveau modèle **Universal** (Universel) qui vise à couvrir tous les scénarios. Les modèles mentionnés ci-dessus resteront disponibles publiquement.

Ensuite, dans la liste déroulante **Acoustic Data** (données acoustiques), sélectionnez les données acoustiques que vous souhaitez utiliser pour effectuer la personnalisation.

![Page Créer un modèle acoustique](media/stt/speech-acoustic-models-create2.png)

Vous pouvez choisir d’effectuer des tests de précision de votre nouveau modèle une fois le traitement terminé. Ceci effectue une évaluation de la conversion parole-texte sur un jeu de données acoustiques spécifié avec le modèle acoustique personnalisé et produit un rapport des résultats. Pour effectuer ce test, cochez la case **Accuracy Testing** (Tests de précision). Sélectionnez ensuite un modèle de langage dans le menu déroulant. Si vous n’avez pas créé de modèle de langage personnalisé, seuls les modèles de langage de base s’affichent dans la liste déroulante. Pour sélectionner le modèle de langage plus approprié, consultez [Tutoriel : Créer un modèle de langage personnalisé](how-to-customize-language-model.md).

Enfin, sélectionnez le jeu de données acoustiques que vous voulez utiliser pour évaluer le modèle personnalisé. Si vous effectuez des tests de précision, il est important de sélectionner des données acoustiques qui sont différentes de celles utilisées pour la création du modèle, de façon à vous faire une idée réaliste des performances du modèle. Des tests de précision sur les données d’apprentissage ne vous permettent pas d’évaluer comment le modèle adapté fonctionne dans des conditions réelles. Le résultat sera trop optimiste. Notez également que les tests de précision sont limités à 1 000 énoncés. Si le jeu de données acoustiques pour le test est plus grand, seuls les 1 000 premiers énoncés sont évalués.

Quand vous êtes prêt à commencer le processus de personnalisation, cliquez sur **Create** (Créer).

La table des modèles acoustiques affiche une nouvelle entrée qui correspond à ce nouveau modèle. La table affiche également l’état du processus : *Waiting* (en attente), *Processing* (en cours de traitement), ou *Complete* (terminé).

![Page des modèles acoustiques](media/stt/speech-acoustic-models-creating.png)

## <a name="next-steps"></a>Étapes suivantes

- [Obtenir votre abonnement d’essai gratuit à Speech Service](https://azure.microsoft.com/try/cognitive-services/)
- [Reconnaissance vocale dans C#](quickstart-csharp-dotnet-windows.md)
- [Exemples de données Git](https://github.com/Microsoft/Cognitive-Custom-Speech-Service)
