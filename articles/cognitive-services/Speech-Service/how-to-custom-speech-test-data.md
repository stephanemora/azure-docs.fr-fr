---
title: Préparer des données de test pour Custom Speech - Service Speech
titleSuffix: Azure Cognitive Services
description: Qu’il s’agisse de tester la précision de la reconnaissance vocale Microsoft ou d’entraîner vos propres modèles, vous avez besoin de données (sous forme de contenu audio et/ou de texte). Dans cette page, nous nous intéressons aux types de données, à la façon dont ils sont utilisés et à leur gestion.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: ba95723e62cec9708684665a9d141b1e39ccb831
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74951834"
---
# <a name="prepare-data-for-custom-speech"></a>Préparer des données pour Custom Speech

Qu’il s’agisse de tester la précision de la reconnaissance vocale Microsoft ou d’entraîner vos propres modèles, vous avez besoin de données sous forme de contenu audio et de texte. Dans cette page, nous nous intéressons aux types de données, à la façon dont ils sont utilisés et à leur gestion.

## <a name="data-types"></a>Types de données

Ce tableau liste les types de données acceptés, les cas d’utilisation pour chacun d’eux ainsi que la quantité recommandée. Pour créer un modèle, vous n’avez pas besoin de chaque type de données. Les données nécessaires varient selon que vous créez un test ou entraînez un modèle.

| Type de données | Utilisé pour les tests | Quantité recommandée | Utilisé pour l’entraînement | Quantité recommandée |
|-----------|-----------------|----------|-------------------|----------|
| [Audio](#audio-data-for-testing) | OUI<br>Utilisé pour l’inspection visuelle | 5 fichiers audio et plus | Non | n/a |
| [Transcriptions audio + étiquetées à la main](#audio--human-labeled-transcript-data-for-testingtraining) | OUI<br>Utilisé pour évaluer la précision | 0,5 – 5 heures de contenu audio | OUI | 1 – 1 000 heures de contenu audio |
| [Texte associé](#related-text-data-for-training) | Non | n/a | OUI | 1 – 200 Mo de texte associé |

Les fichiers doivent être regroupées par type dans un jeu de données et chargés sous forme de fichier zip. Chaque jeu de données ne peut contenir qu’un seul type de données.

## <a name="upload-data"></a>Télécharger des données

Une fois que vous êtes prêt à charger vos données, accédez au [portail Custom Speech](https://speech.microsoft.com/customspeech), puis cliquez sur **Upload data** (Charger des données) pour lancer l’Assistant et créer votre premier jeu de données. Vous êtes alors invité à sélectionner un type de données vocales pour votre jeu de données avant d’être autorisé à charger vos données.

![Sélectionner du contenu audio à partir du portail Speech](./media/custom-speech/custom-speech-select-audio.png)

Chaque jeu de données que vous chargez doit respecter les exigences associé au type de données choisi. Il est important de bien formater les données avant de les charger pour qu’elles soient correctement traitées par le service Custom Speech. Les exigences sont listées dans les sections suivantes.

Une fois que votre jeu de données est chargé, vous disposez de plusieurs options :

* Vous pouvez accéder à l’onglet **Testing** (Tests) et inspecter visuellement du contenu audio uniquement ou du contenu audio + données de transcription étiquetées à la main.
* Vous pouvez accéder à l’onglet **Training** (Entraînement) et utiliser du contenu audio + données de transcription étiquetées à la main ou des données de texte associées pour entraîner un modèle personnalisé.

## <a name="audio-data-for-testing"></a>Données audio pour tester

Les données audio sont optimales pour tester la précision du modèle de reconnaissance vocale de référence de Microsoft ou d’un modèle personnalisé. Gardez à l’esprit que les données audio sont utilisées pour inspecter la précision de la reconnaissance vocale par rapport à un modèle spécifique. Si vous cherchez à quantifier la précision d’un modèle, utilisez du [contenu audio + données de transcription étiquetés à la main](#audio--human-labeled-transcript-data-for-testingtraining).

Servez-vous de ce tableau pour vérifier que le format de vos fichiers audio convient pour une utilisation avec Custom Speech :

| Propriété | Valeur |
|----------|-------|
| Format de fichier | RIFF (WAV) |
| Échantillonnage | 8 000 Hz ou 16 000 Hz |
| Canaux | 1 (mono) |
| Longueur maximale par fichier audio | 2 heures |
| Format d’échantillonnage | PCM, 16 bits |
| Format d’archive | .zip |
| Taille d’archive maximale | 2 Go |

> [!TIP]
> Lors du chargement de données de formation et de test, la taille du fichier .zip ne peut pas dépasser 2 Go. Si vous avez besoin de plus de données pour la formation et les tests, divisez-le en plusieurs fichiers .zip et chargez-les séparément. Plus tard, vous pouvez choisir d’effectuer l’apprentissage et les tests à partir de *plusieurs* jeux de données.

Si votre fichier audio ne satisfait pas ces propriétés ou si vous voulez vérifier s’il les respecte, nous vous suggérons de télécharger [sox](http://sox.sourceforge.net) pour vérifier ou convertir le fichier audio. Vous trouverez ci-dessous quelques exemples de la façon dont chacune de ces activités peut être effectuée via la ligne de commande :

| Activité | Description | Commande sox |
|----------|-------------|-------------|
| Vérifier le format audio | Utilisez cette commande pour vérifier le format du fichier audio. | `sox --i <filename>` |
| Convertir le format audio | Utilisez cette commande pour convertir le fichier audio en canal unique, 16 bits, 16 KHz. | `sox <input> -b 16 -e signed-integer -c 1 -r 16k -t wav <output>.wav` |

## <a name="audio--human-labeled-transcript-data-for-testingtraining"></a>Contenu audio + données de transcription étiquetées à la main pour tester/entraîner

Pour mesurer la précision de la reconnaissance vocale de Microsoft pendant le traitement de vos fichiers audio, vous devez fournir des transcriptions étiquetées à la main (mot par mot) pour effectuer la comparaison. Si la transcription étiquetée à la main prend souvent beaucoup de temps, elle est nécessaire pour évaluer la précision et entraîner le modèle pour vos cas d’usage. Gardez à l’esprit que les améliorations de la reconnaissance seront proportionnelles à la qualité des données fournies. C’est pourquoi il est important de charger uniquement des transcriptions de grande qualité.

| Propriété | Valeur |
|----------|-------|
| Format de fichier | RIFF (WAV) |
| Échantillonnage | 8 000 Hz ou 16 000 Hz |
| Canaux | 1 (mono) |
| Longueur maximale par fichier audio | 60 s |
| Format d’échantillonnage | PCM, 16 bits |
| Format d’archive | .zip |
| Taille maximale de zip | 2 Go |

> [!TIP]
> Lors du chargement de données de formation et de test, la taille du fichier .zip ne peut pas dépasser 2 Go. Si vous avez besoin de plus de données pour la formation et les tests, divisez-le en plusieurs fichiers .zip et chargez-les séparément. Plus tard, vous pouvez choisir d’effectuer l’apprentissage et les tests à partir de *plusieurs* jeux de données.

Pour résoudre les problèmes comme la suppression ou la substitution de mots, une quantité importante de données est nécessaire pour améliorer la reconnaissance. En règle générale, il est recommandé de fournir des transcriptions mot par mot pour environ 10 à 1 000 heures de contenu audio. Les transcriptions de tous les fichiers WAV doivent se trouver dans un seul fichier en texte brut. Chaque ligne du fichier de transcription doit contenir le nom d’un des fichiers audio, suivi de la transcription correspondante. Le nom de fichier et la transcription doivent être séparés par une tabulation (\t).

  Par exemple :
```
  speech01.wav  speech recognition is awesome
  speech02.wav  the quick brown fox jumped all over the place
  speech03.wav  the lazy dog was not amused
```
> [!NOTE]
> La transcription doit être encodée au format UTF-8 marque d'ordre d'octet (BOM).

Les transcriptions doivent être en texte normalisé pour pouvoir être traitées par le système. Une normalisation importante doit toutefois être effectuée par l’utilisateur _avant_ de charger les données dans le Studio Speech. Pour savoir quelle langue utiliser pour la préparation de vos transcriptions, consultez [Guide pratique pour créer une transcription étiquetée à la main](how-to-custom-speech-human-labeled-transcriptions.md).

Une fois que vous avez collecté vos fichiers audio et les transcriptions correspondantes, vous devez les empaqueter dans un même fichier .zip avant de charger ce dernier vers le [portail Custom Speech](https://speech.microsoft.com/customspeech). Voici un exemple de jeu de données constitué de trois fichiers audio et d’un fichier de transcriptions étiquetées à la main :

![Sélectionner du contenu audio à partir du portail Speech](./media/custom-speech/custom-speech-audio-transcript-pairs.png)

## <a name="related-text-data-for-training"></a>Données texte associées pour l’entraînement

Si vous voulez faire en sorte que des noms de produits ou des fonctionnalités uniques soient reconnus correctement, il est important d’inclure des données texte associées pour l’entraînement. Deux types de données texte associées peuvent être fournis pour améliorer la reconnaissance :

| Type de données | Comment ces données améliorent la reconnaissance |
|-----------|------------------------------------|
| Énoncés et/ou phrases | Peuvent améliorer la précision de la reconnaissance des noms de produits ou du jargon d’un domaine spécifique dans le contexte d’une phrase. |
| Prononciations | Peuvent améliorer la prononciation des termes peu communs, des acronymes ou d’autres mots dont la prononciation n’est pas définie. |

Les énoncés peuvent être fournis dans un ou plusieurs fichiers texte. Plus les données texte seront proches de ce qui est prononcé, plus il est probable que la précision s’améliorera. Les prononciations doivent être fournies sous forme de fichier texte unique. L’ensemble peut être empaqueté dans un même fichier zip et chargé vers le [portail Custom Speech](https://speech.microsoft.com/customspeech).

### <a name="guidelines-to-create-an-utterances-file"></a>Recommandations pour créer un fichier d’énoncés

Pour créer un modèle personnalisé utilisant du texte associé, vous devez fournir une liste d’exemples d’énoncés. Ces énoncés ne doivent pas être nécessairement des phrases complètes ou grammaticalement correctes. En revanche, elles doivent refléter précisément l’entrée parlée que vous espérez en production. Pour donner du poids à certains termes, vous pouvez ajouter plusieurs phrases au fichier de données associées qui contient ces termes spécifiques.

Servez-vous de ce tableau pour vérifier que votre fichier de données associées présente le bon format pour les énoncés :

| Propriété | Valeur |
|----------|-------|
| Encodage de texte | UTF-8 BOM |
| Nb d’énoncés par ligne | 1 |
| Taille maximale du fichier | 200 Mo |

Par ailleurs, vous devez prendre en compte les restrictions suivantes :

* Évitez de répéter un même caractère plus de 4 fois. Par exemple : « aaaa » ou « uuuu ».
* N’utilisez pas de caractères spéciaux ou de caractères UTF-8 au-dessus de U+00A1.
* Les URI sont rejetés.

### <a name="guidelines-to-create-a-pronunciation-file"></a>Recommandations pour créer un fichier de prononciation

Si vos utilisateurs sont appelés à rencontrer ou à utiliser des termes peu communs qui se prononcent d’une façon particulière, vous pouvez fournir un fichier de prononciation personnalisé pour améliorer la reconnaissance.

> [!IMPORTANT]
> Il est déconseillé d’utiliser cette fonctionnalité pour modifier la prononciation des mots communs.

Ce tableau contient des exemples d’énoncés oraux et une prononciation personnalisée pour chacun d’eux :

| Forme reconnue/affichée | Forme orale (en anglais) |
|--------------|--------------------------|
| 3CPO | trois c p o |  
| CNTK (Computational Network Toolkit de Microsoft Research) | c n t k |
| IEEE | i triple e |

La forme orale est la séquence phonétique décomposée. Elle peut être constituée de lettres, de mots, de syllabes ou d’une combinaison des trois.

La prononciation personnalisée est disponible en anglais (en-US) et en allemand (de-DE). Ce tableau présente les caractères pris en charge par langue :

| Langage | Paramètres régionaux | Caractères |
|----------|--------|------------|
| Anglais | en-US | a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z |
| Allemand | de-DE | ä, ö, ü, a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z |

Servez-vous de ce tableau pour vérifier que votre fichier de données associées présente le bon format pour les prononciations. Les fichiers de prononciation sont petits et doivent se limiter à quelques Ko.

| Propriété | Valeur |
|----------|-------|
| Encodage de texte | UTF-8 BOM (ANSI est également pris en charge pour l’anglais) |
| Nbre de prononciations par ligne | 1 |
| Taille maximale du fichier | 1 Mo (1 Ko pour le niveau gratuit) |

## <a name="next-steps"></a>Étapes suivantes

* [Inspecter les données](how-to-custom-speech-inspect-data.md)
* [Évaluer les données](how-to-custom-speech-evaluate-data.md)
* [Entraîner un modèle](how-to-custom-speech-train-model.md)
* [Déployer un modèle](how-to-custom-speech-deploy-model.md)
