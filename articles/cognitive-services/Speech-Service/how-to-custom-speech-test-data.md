---
title: Préparer les données de test pour la reconnaissance vocale personnalisée - Services de reconnaissance vocale
titlesuffix: Azure Cognitive Services
description: Si vous testez pour voir la reconnaissance vocale Microsoft la précision est ou former vos propres modèles, vous aurez besoin des données (sous la forme de texte et/ou audio). Dans cette page, nous abordons les types de données, comment elles sont utilisées et comment les gérer.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: af801600eebed7c0d4ff01dd1edf01fa595840eb
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65785770"
---
# <a name="prepare-data-for-custom-speech"></a>Préparer des données pour la reconnaissance vocale personnalisé

Si vous testez pour voir la reconnaissance vocale Microsoft la précision est ou former vos propres modèles, vous aurez besoin des données sous la forme d’audio et de texte. Dans cette page, nous abordons les types de données, comment elles sont utilisées et comment les gérer.

## <a name="data-types"></a>Types de données

Ce tableau répertorie les types de données acceptées, lors de chaque type de données doit être utilisé et la quantité recommandée. Pas de chaque type de données est nécessaire pour créer un modèle. Exigences de données varient selon que vous êtes de création d’un test ou d’apprentissage d’un modèle.

| Type de données | Utiliser des tests | Quantité | Utilisé pour l’apprentissage | Quantité |
|-----------|-----------------|----------|-------------------|----------|
| [Audio](#audio-data-for-testing) | Oui<br>Utilisé pour l’inspection visual | fichiers audio 5 + | Non | N/a |
| [Transcriptions audio + étiquetés humaines](#audio--human-labeled-transcript-data-for-testingtraining) | Oui<br>Utilisé pour évaluer la précision | 0,5 - 5 heures d’audio | Oui | 1 - 1 000 heures de l’audio |
| [Texte associé](##related-text-data-for-training) | Non | N/a | Oui | 1-200 Mo de texte correspondante |

Les fichiers doivent être regroupées par type dans un dataset ou téléchargés dans un fichier zip. Chaque jeu de données ne peut contenir qu’un seul type de données.

## <a name="upload-data"></a>Télécharger des données

Lorsque vous êtes prêt à charger vos données, cliquez sur **charger des données** pour lancer l’Assistant et créer votre premier jeu de données. Vous allez être invité à sélectionner un type de données de reconnaissance vocale pour votre jeu de données, avant ce qui vous permet de charger vos données.

![Sélectionnez audio à partir du portail de reconnaissance vocale](./media/custom-speech/custom-speech-select-audio.png)

Chaque jeu de données que vous téléchargez doit respecter la configuration requise pour le type de données que vous choisissez. Il est important de mettre correctement en forme vos données avant d’être téléchargée. Cela garantit que les données vont être traitées avec précision par le service de reconnaissance vocale personnalisé. Configuration requise est répertoriée dans les sections suivantes.

Une fois que votre jeu de données est chargé, vous disposez de plusieurs options :

* Vous pouvez accéder à la **test** onglet et inspecter visuellement audio uniquement ou données de transcription audio + étiquetés humaines.
* Vous pouvez accéder à la **formation** onglet et nous les données de transcription audio + humaine ou données texte associées pour former un modèle personnalisé.

## <a name="audio-data-for-testing"></a>Données audio pour le test

Données audio sont optimales pour tester la précision du modèle de reconnaissance vocale de Microsoft de référence ou un modèle personnalisé. N’oubliez pas, les données audio sont utilisées pour inspecter l’exactitude de la parole en ce qui concerne les performances d’un modèle spécifique. Si vous avez besoin pour quantifier la précision d’un modèle, utilisez [les données de transcription audio + étiquetés humaines](#audio--human-labeled-transcript-data-for-testingtraining).

Utilisez ce tableau pour vous assurer que vos fichiers audio sont correctement mis en forme pour une utilisation avec la reconnaissance vocale personnalisé :

| Propriété | Valeur |
|----------|-------|
| Format de fichier | RIFF (WAV) |
| Taux d'échantillonnage | 8 000 ou 16 000 Hz |
| Canaux | 1 (mono) |
| Longueur maximale par audio | 2 heures |
| Format d’échantillonnage | PCM, 16 bits |
| Format d’archive | .zip |
| Taille d’archive maximale | 2 Go |

Si votre audio ne satisfait pas ces propriétés, ou vous souhaitez vérifier s’il existe, nous vous suggérons de téléchargement [sox](http://sox.sourceforge.net) pour vérifier ou de convertir l’audio. Voici quelques exemples de la façon dont chacune de ces activités peut être effectuée dans la ligne de commande :

| Activité | Description  | Commande de SOX |
|----------|-------------|-------------|
| Vérifiez le format audio | Cette commande permet de vérifier le format de fichier audio. | `soxi <filename>.wav` |
| Convertir le format audio | Cette commande permet de convertir le fichier audio monocanal, 16 bits, 48 KHz. | `sox <filename>.wav -b 16 -3 signed-integer -c l -r 48k -t wav <filename>.wav` |

## <a name="audio--human-labeled-transcript-data-for-testingtraining"></a>Données de transcription audio + étiquetés humaines pour la formation/test

Pour mesurer la précision de la précision de reconnaissance vocale de Microsoft lors du traitement de vos fichiers audio, vous devez fournir des transcriptions humaines étiquetés (mot par mot) pour la comparaison. Alors que la transcription étiquetés humain est souvent beaucoup de temps, il est nécessaire pour évaluer la précision et former le modèle pour vos cas d’usage. N’oubliez pas, les améliorations de reconnaissance n’est aussi bien que les données fournies. Pour cette raison, il est important que les transcriptions de haute qualité seulement sont chargées.  

| Propriété | Valeur |
|----------|-------|
| Format de fichier | RIFF (WAV) |
| Taux d'échantillonnage | 8 000 ou 16 000 Hz |
| Canaux | 1 (mono) |
| Longueur maximale par audio | 60 s |
| Format d’échantillonnage | PCM, 16 bits |
| Format d’archive | .zip |
| Taille maximale de zip | 2 Go |

Pour résoudre les problèmes tels que la suppression de word ou de substitution, une quantité importante de données est requis pour améliorer la reconnaissance. En règle générale, il est recommandé de fournir des transcriptions de mot par mot pour environ 10 à 1 000 heures de contenu audio. Les transcriptions de tous les fichiers WAV doivent se trouver dans un seul fichier en texte brut. Chaque ligne du fichier de transcription doit contenir le nom d’un des fichiers audio, suivi de la transcription correspondante. Le nom de fichier et la transcription doivent être séparés par une tabulation (\t).

  Exemple :
```
  speech01.wav  speech recognition is awesome
  speech02.wav  the quick brown fox jumped all over the place
  speech03.wav  the lazy dog was not amused
```
> [!NOTE]
> La transcription doit être encodée au format UTF-8 marque d'ordre d'octet (BOM).

Les transcriptions doivent être en texte normalisé pour pouvoir être traitées par le système. Une normalisation importante doit toutefois être effectuée par l’utilisateur _avant_ de charger les données dans Custom Speech Service. Pour la langue appropriée à utiliser lorsque vous préparez votre transcriptions, consultez [comment créer une transcription étiquetés humaines](how-to-custom-speech-human-labeled-transcriptions.md)

Une fois que vous avez collecté des fichiers audio et transcriptions correspondantes, ils doivent être empaquetés en tant qu’un seul fichier .zip avant le chargement dans le portail de reconnaissance vocale personnalisé. Il s’agit d’un jeu de données d’exemple avec trois fichiers audio et d’un fichier de transcription étiquetés humaines :

![Sélectionnez audio à partir du portail de reconnaissance vocale](./media/custom-speech/custom-speech-audio-transcript-pairs.png)

## <a name="related-text-data-for-training"></a>Données texte associées pour l’apprentissage

Si vous avez des noms de produits ou des fonctionnalités qui sont uniques, et vous souhaitez vous assurer qu’ils sont reconnus correctement, il est important d’inclure des données texte associées pour l’apprentissage. Deux types de données de texte correspondante peuvent être fournis pour améliorer la reconnaissance :

| Type de données | Comment ces données améliorent la reconnaissance |
|-----------|------------------------------------|
| Énoncés et/ou des phrases | Ils peuvent améliorer la précision lors de la reconnaissance des noms de produits, ou un vocabulaire spécifiques du secteur dans le contexte d’une phrase. |
| Prononciations | Ces peuvent améliorer la prononciation des termes rares, acronymes ni d’autres mots avec prononciations non définies. |

Énoncés peuvent être fournis en tant qu’un seul ou plusieurs fichiers texte. Plus le texte de données concernent à ce que seront énoncé, plus la probabilité que la précision est améliorée. Prononciations doivent être fournies comme un seul fichier texte. Tout peut être empaqueté en tant qu’un seul fichier zip et téléchargé sur le portail de reconnaissance vocale personnalisé.

### <a name="guidelines-to-create-an-utterances-file"></a>Instructions pour créer un fichier énoncés

Pour créer un modèle personnalisé à l’aide de texte correspondante, vous devez fournir une liste des énoncés d’exemple. Ces énoncés n’avez pas besoin être phrases complètes ou grammaticalement incorrect, mais ils doivent refléter précisément l’entrée parlée souhaitées en production. Si vous souhaitez que certains termes ont augmenté de poids, vous pouvez ajouter plusieurs phrases à votre fichier de données associées qui incluent ces termes spécifiques.

Utilisez ce tableau pour vous assurer que votre fichier de données associées pour énoncés est mise en forme correctement :

| Propriété | Valeur |
|----------|-------|
| Encodage de texte | UTF-8 BOM |
| Nb d’énoncés par ligne | 1 |
| Taille maximale du fichier | 200 Mo |

En outre, vous allez peut-être pour prendre en compte les restrictions suivantes :

* Éviter de répéter des quatre fois plus de caractères. Par exemple : « aaaa » ou « uuuu ».
* N’utilisez pas les caractères spéciaux ou des caractères UTF-8 ci-dessus U + 00 a 1.
* URI sont rejetées.

### <a name="guidelines-to-create-a-pronunciation-file"></a>Instructions pour créer un fichier de prononciation

S’il existe des conditions rares sans prononciations standards qui rencontrent ou utiliser vos utilisateurs, vous pouvez fournir un fichier de prononciation personnalisé pour améliorer la reconnaissance.

> [!IMPORTANT]
> Il n’est pas recommandé d’utiliser cette fonctionnalité pour modifier la prononciation des mots courants.

Cela inclut des exemples d’un énoncé prononcé et une prononciation personnalisée pour chaque :

| Forme orale (en anglais) | Reconnu/affiche un formulaire |
|--------------|--------------------------|
| trois c p o | 3CPO |  
| t n c k | CNTK |
| i triple e | IEEE |

La forme parlée est la séquence phonétique explicitée. Peut être composé de lettres, des mots, syllabes ou une combinaison des trois.

Prononciation personnalisée est disponible en anglais (en-US) et en français (fr-fr). Ce tableau présente les caractères pris en charge par le langage :

| Langue | Paramètres régionaux | Personnages |
|----------|--------|------------|
| Anglais | fr-FR | a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z |
| Allemand | de-DE | ä, ö, ü, a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z |

Cette table permet de vous assurer que votre fichier de données associées pour prononciations est mise en forme correctement. Fichiers de prononciation sont petits et ne doivent pas dépasser quelques bases de connaissances.

| Propriété | Valeur |
|----------|-------|
| Encodage de texte | UTF-8 BOM (ANSI est également pris en charge pour l’anglais) |
| # des prononciations par ligne | 1 |
| Taille maximale du fichier | 1 Mo (1 Ko pour le niveau gratuit) |

## <a name="next-steps"></a>Étapes suivantes

* [Inspectez vos données](how-to-custom-speech-inspect-data.md)
* [Évaluer vos données](how-to-custom-speech-evaluate-data.md)
* [Formation d’un modèle](how-to-custom-speech-train-model.md)
* [Déployer votre modèle](how-to-custom-speech-deploy-model.md)
