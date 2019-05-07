---
title: Comment préparer les données pour la voix personnalisée - Services de reconnaissance vocale
titlesuffix: Azure Cognitive Services
description: Créer une voix personnalisée pour votre marque avec les Services de reconnaissance vocale de Azure. Vous fournissez des enregistrements de studio et les scripts associés, le service génère un modèle de voix uniques adaptée à la voix enregistrée. Utilisez cette voix de synthèse vocale dans vos produits, les outils et les applications.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: erhopf
ms.openlocfilehash: 18e1bb486c47baf7648a74e31451e2db73f72250
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65156858"
---
# <a name="prepare-data-to-create-a-custom-voice"></a>Préparer des données pour créer une voix personnalisée

Lorsque vous êtes prêt à créer une voix de synthèse vocale personnalisée pour votre application, la première étape consiste à rassembler des enregistrements audio et les scripts associés pour démarrer l’apprentissage du modèle de voix. Le service utilise ces données pour créer une voix unique configurée en fonction de la voix dans les enregistrements. Une fois que vous avez formé la voix, vous pouvez commencer la synthèse vocale dans vos applications.

Vous pouvez commencer avec une petite quantité de données pour créer une preuve de concept. Toutefois, les plus de données que vous fournissez, le plus naturel paraître votre voix personnalisé. Avant que vous pouvez former votre propre modèle de voix de synthèse vocale, vous aurez besoin des enregistrements audio et les transcriptions de texte associée. Dans cette page, nous allons examiner les types de données, comment elles sont utilisées et comment gérer chacune.

## <a name="data-types"></a>Types de données

Un jeu de données de formation de voix inclut des enregistrements audio et un fichier texte avec les transcriptions associés. Chaque fichier audio doit contenir un énoncé unique (une phrase unique ou un seul activer pour un système de la boîte de dialogue) et être inférieure à 15 secondes.

Dans certains cas, vous n’avez ne peut-être pas le bon jeu de données prêt et souhaitez tester la formation vocaux personnalisés avec des fichiers audio disponibles, courts ou longs, avec ou sans les relevés de notes. Nous fournissons des outils (bêta) pour vous aider à segmenter votre audio en énoncés et préparer les relevés de notes à l’aide de la [API de Transcription Batch](batch-transcription.md).

Ce tableau répertorie les types de données et la façon dont chacun est utilisé pour créer un modèle personnalisé de voix de synthèse vocale.

| Type de données | Description | Quand utiliser | Service supplémentaire requis | Quantité pour former un modèle | Paramètres régionaux |
| --------- | ----------- | ----------- | --------------------------- | ----------------------------- | --------- |
| **Énoncés individuels + transcription correspondante** | Une collection (.zip) de fichiers audio (.wav) comme énoncés individuels. Chaque fichier audio doit être de 15 secondes ou moins, associée à une transcription de mise en forme (.txt). | Enregistrements professionnels avec correspondance relevés de notes | Prêt pour l’apprentissage. | Pas d’exigence pour en-US et zh-CN. Plus de 2 000 + énoncés distinctes pour les autres paramètres régionaux. | Tous les paramètres régionaux de voix de personnalisé |
| **Audio long + transcription (bêta)** | Une collection (.zip) de longs, non segmentés fichiers audio (plus de 20 secondes), associée à une transcription (.txt) qui contient tous les mots prononcés. | Vous avez des fichiers audio et transcriptions correspondantes, mais ils ne sont pas placés dans énoncés. | Segmentation (à l’aide de la transcription de batch).<br>Transformation de format audio si nécessaire. | Pas d’exigence pour en-US et zh-CN. | `en-US` et `zh-CN` |
| **Audio (bêta)** | Collection (.zip) de fichiers audio sans une transcription. | Vous avez uniquement des fichiers audio disponibles, sans les relevés de notes. | Segmentation + génération de transcription (à l’aide de la transcription de batch).<br>Transformation de format audio si nécessaire.| Pas d’exigence pour `en-US` et `zh-CN`. | `en-US` et `zh-CN` |

Les fichiers doivent être regroupées par type dans un dataset ou téléchargés dans un fichier zip. Chaque jeu de données ne peut contenir qu’un seul type de données.

> [!NOTE]
> Le nombre maximal de jeux de données autorisés à être importé par abonnement est .zip 10 fichiers gratuitement aux utilisateurs d’abonnement (F0) et 500 pour les utilisateurs de l’abonnement standard (S0).

## <a name="individual-utterances--matching-transcript"></a>Énoncés individuels + transcription correspondante

Vous pouvez préparer des enregistrements d’énoncés individuels et la transcription correspondante de deux manières. Soit écrire un script et l’avez lu par un talent de voix ou utiliser audio disponibles publiquement et retranscrire au texte. Dans ce dernier cas, éliminez les disfluences dans les fichiers audio telles que les « euh » et autres sons de remplissage, bégaiements, mots marmonnés ou erreurs de prononciation.

Pour produire une police de la bonne voix, créer les enregistrements dans une salle silencieuse avec un microphone de haute qualité. Volume cohérent, vitesse, en parlant de tonalité et expressifs tics de reconnaissance vocale sont essentielles.

> [!TIP]
> Pour créer une voix à des fins de production, nous vous recommandons de faire appel aux services d’un studio d’enregistrement et d’une voix professionnelle. Pour plus d’informations, consultez le [Guide pratique pour enregistrer des exemples de voix pour une voix personnalisée](https://review.docs.microsoft.com/azure/cognitive-services/speech-service/record-custom-voice-samples).

### <a name="audio-files"></a>Fichiers audio

Chaque fichier audio doit contenir un énoncé unique (une phrase unique ou un seul activer d’un système de la boîte de dialogue), inférieur à 15 secondes. Tous les fichiers doivent être dans la même langue parlée. Voix de synthèse vocale personnalisée multilingue n'est pas pris en charge, à l’exception de la bilingues chinois-anglais. Chaque fichier audio doit avoir un nom de fichier numérique unique avec l’extension de nom de fichier .wav.

Suivez ces instructions lorsque vous préparez des données audio.

| Propriété | Valeur |
| -------- | ----- |
| Format de fichier | RIFF (.wav), regroupé dans un fichier .zip |
| Taux d’échantillonnage | au moins 16 000 Hz |
| Format d’échantillonnage | PCM, 16 bits |
| Nom de fichier | Numérique, avec une extension .wav. Aucun nom de fichier en double autorisé. |
| Longueur audio | Moins de 15 secondes |
| Format d’archive | .zip |
| Taille d’archive maximale | 200 Mo |

> [!NOTE]
> fichiers .wav avec un taux d’échantillonnage inférieur à 16 000 Hz sont rejetées. Si un fichier .zip contient les fichiers .wav avec différents taux d’échantillonnage, seuls les égal à ou supérieur à 16 000 Hz est importé. Actuellement, le portail importe les archives .zip jusqu’à 200 Mo. Toutefois, il est possible de charger plusieurs archives.

### <a name="transcripts"></a>Transcriptions

Le fichier de transcription est un fichier texte brut. Utilisez ces instructions pour préparer votre transcriptions.

| Propriété | Valeur |
| -------- | ----- |
| Format de fichier | Texte brut (.txt) |
| Format d’encodage | ANSI/ASCII, UTF-8, UTF-8-BOM, UTF-16-LE ou UTF-16-BE. Pour zh-CN, les encodages ANSI/ASCII et UTF-8 ne sont pas pris en charge. |
| Nb d’énoncés par ligne | **Un** -chaque ligne du fichier de transcription doit contenir le nom d’un des fichiers audio, suivies de la transcription correspondante. Le nom de fichier et la transcription doivent être séparés par une tabulation (\t). |
| Taille maximale du fichier | 50 Mo |

Voici un exemple de la façon dont les transcriptions sont organisées énoncé par énoncé dans un fichier .txt :

```
0000000001[tab] This is the waistline, and it's falling.
0000000002[tab] We have trouble scoring.
0000000003[tab] It was Janet Maslin.
```
Il est important que les transcriptions sont 100 % des transcriptions précis de l’audio correspondante. Erreurs dans les transcriptions introduira une perte de qualité pendant l’apprentissage.

> [!TIP]
> Lors de la génération de production voix, énoncés sélectionnez (ou écrire des scripts) qui prennent en compte la couverture phonétique et l’efficacité. Des difficultés à obtenir les résultats que vous souhaitez ? [Contactez la voix personnalisée](mailto:speechsupport@microsoft.com) team en savoir plus sur nous avoir, consultez.

## <a name="long-audio--transcript-beta"></a>Audio long + transcription (bêta)

Dans certains cas, vous pouvez de pas segmentés audio disponible. Nous fournissons un service (bêta) via le portail vocaux personnalisés pour vous aider à segmenter les fichiers audio long et créer des transcriptions. Gardez à l’esprit, ce service est facturé vers l’utilisation de votre abonnement de la parole-texte.

> [!NOTE]
> Le service de segmentation de l’audio de longue durée s’appuieront sur la fonctionnalité de transcription de lot de la parole-texte, qui prend uniquement en charge les utilisateurs d’abonnement standard (S0). Lors du traitement de la segmentation, vos fichiers audio et les transcriptions seront également être envoyées au service de reconnaissance vocale personnalisée pour affiner le modèle de reconnaissance afin de la précision peut être améliorée pour vos données. Aucune donnée n’est conservées pendant ce processus. Une fois que la segmentation est terminée, uniquement les énoncés segmentés et leurs transcriptions de mappage seront stockées pour votre téléchargement et la formation.

### <a name="audio-files"></a>Fichiers audio

Suivez ces instructions lors de la préparation de l’audio pour la segmentation.

| Propriété | Valeur |
| -------- | ----- |
| Format de fichier | RIFF (.wav) avec un taux d’échantillonnage d’au moins 16 khz 16 bits dans PCM ou .mp3 avec une vitesse de transmission d’au moins 256 Kbits/s, regroupées dans un fichier .zip |
| Nom de fichier | Uniquement des caractères ASCII. Caractères Unicode dans le nom échoue (par exemple, les caractères chinois, ou des symboles tels que « : »). Aucun nom en double n’est autorisée. |
| Longueur audio | Plus de 20 secondes |
| Format d’archive | .zip |
| Taille d’archive maximale | 200 Mo |

Tous les fichiers audio doivent être regroupés dans un fichier zip. Il s’agit de OK à placer des fichiers .wav et .mp3 dans un zip audio, mais aucun sous-dossier n’est autorisée dans le fichier zip. Par exemple, vous pouvez télécharger un fichier zip contenant un fichier audio nommés 'kingstory.wav', 45-seconde-long et un autre un nommé « queenstory.mp3 », 200-seconde-type long, sans tous les sous-dossiers. Tous les fichiers .mp3 seront transformés au format .wav après le traitement.

### <a name="transcripts"></a>Transcriptions

Relevés de notes doivent être préparées pour les spécifications répertoriées dans ce tableau. Chaque fichier audio doit être mis en correspondance avec une transcription.

| Propriété | Valeur |
| -------- | ----- |
| Format de fichier | Texte brut (.txt), regroupées dans un fichier zip |
| Nom de fichier | Utilisez le même nom que le fichier audio correspondant |
| Format d’encodage | UTF-8-BOM uniquement |
| Nb d’énoncés par ligne | Aucune limite |
| Taille maximale du fichier | 50 MILLIONS |

Tous les fichiers de relevés de notes dans ce type de données doivent être regroupés dans un fichier zip. Aucun sous-dossier n’est autorisée dans le fichier zip. Par exemple, vous avez chargé un fichier zip contenant un fichier audio nommé « kingstory.wav », 45 secondes, et un autre un nommé « queenstory.mp3 », 200 secondes long. Vous devez charger un autre fichier zip qui contient deux relevés de notes, un nommé « kingstory.txt », autres un « queenstory.txt ». Au sein de chaque fichier texte brut, vous fournirez la transcription correcte complet de l’audio correspondant.

Une fois votre jeu de données est correctement chargé, nous vous permettra de segmenter le fichier audio en énoncés basés sur la transcription fournie. Vous pouvez vérifier les énoncés segmentés et les transcriptions correspondantes en téléchargeant le jeu de données. ID uniques est attribuées automatiquement à énoncés segmentés. Il est important de vérifier que les transcriptions que vous fournissez sont précis à 100 %. Erreurs dans les transcriptions peuvent réduire la précision lors de la segmentation audio et introduire une perte de qualité dans la phase de formation qui intervient plus tard.

## <a name="audio-only-beta"></a>Audio (bêta)

Si vous n’avez transcriptions pour vos enregistrements audio, utilisez le **Audio uniquement** option permettant de charger vos données. Notre système peut vous aider à segmenter et transcrire vos fichiers audio. N’oubliez pas, ce service est comptabilisée dans l’utilisation de votre abonnement de la parole-texte.

Suivez ces instructions lorsque vous préparez des données audio.

> [!NOTE]
> Le service de segmentation de l’audio de longue durée s’appuieront sur la fonctionnalité de transcription de lot de la parole-texte, qui prend uniquement en charge les utilisateurs d’abonnement standard (S0).

| Propriété | Valeur |
| -------- | ----- |
| Format de fichier | RIFF (.wav) avec un taux d’échantillonnage d’au moins 16 khz 16 bits dans PCM ou .mp3 avec une vitesse de transmission d’au moins 256 Kbits/s, regroupées dans un fichier .zip |
| Nom de fichier | Uniquement des caractères ASCII. Caractères Unicode dans le nom échoue (par exemple, les caractères chinois, ou des symboles tels que « : »). Aucun nom en double n’est autorisée. |
| Longueur audio | Plus de 20 secondes |
| Format d’archive | .zip |
| Taille d’archive maximale | 200 Mo |

Tous les fichiers audio doivent être regroupés dans un fichier zip. Aucun sous-dossier n’est autorisée dans le fichier zip. Une fois que votre jeu de données est correctement chargé, nous vous permettra de segmenter le fichier audio en énoncés basés sur notre service de transcription vocale batch. ID uniques est attribuées automatiquement à énoncés segmentés. Correspondance des relevés de notes sera généré via la reconnaissance vocale. Tous les fichiers .mp3 seront transformés au format .wav après le traitement. Vous pouvez vérifier les énoncés segmentés et les transcriptions correspondantes en téléchargeant le jeu de données.

## <a name="next-steps"></a>Étapes suivantes

- [Créer une voix personnalisée](how-to-custom-voice-create-voice.md)
- [Guide : Enregistrer vos exemples de voix](record-custom-voice-samples.md)
