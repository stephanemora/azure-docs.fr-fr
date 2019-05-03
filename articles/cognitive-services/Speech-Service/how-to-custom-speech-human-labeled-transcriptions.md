---
title: Instructions de transcriptions étiquetés humaines - Services de reconnaissance vocale
titlesuffix: Azure Cognitive Services
description: Si vous avez besoin pour améliorer la précision de la reconnaissance, en particulier les problèmes qui se produisent quand les mots sont supprimés ou incorrectement substituées, il allez que vous souhaitez utiliser humaines en partie les transcriptions, ainsi que vos données audio. Quelles sont les transcriptions humaines en partie ? C’est facile, elles sont des transcriptions mot par mot, textuelles d’un fichier audio.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 7f0b467284872f3d936984741c6d092705008a5a
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025921"
---
# <a name="how-to-create-human-labeled-transcriptions"></a>Comment créer des transcriptions étiquetés humaines

Si vous avez besoin pour améliorer la précision de la reconnaissance, en particulier les problèmes qui se produisent quand les mots sont supprimés ou incorrectement substituées, il allez que vous souhaitez utiliser humaines en partie les transcriptions, ainsi que vos données audio. Quelles sont les transcriptions humaines en partie ? C’est facile, elles sont des transcriptions mot par mot, textuelles d’un fichier audio.

Un échantillon volumineux de données de transcription est nécessaire pour améliorer la reconnaissance, nous vous suggérons fournissant comprise entre 10 et 1 000 heures de données de transcription. Dans cette page, nous allons examiner les instructions conçues pour vous aider à créer des transcriptions de haute qualité. Ce guide est divisé par les paramètres régionaux, avec des sections pour anglais (États-Unis), chinois Mandarin et allemand.

## <a name="us-english-en-us"></a>Anglais (États-Unis, en-US)

Humaines en partie les transcriptions audio en anglais doivent être fournies sous forme de texte brut, uniquement à l’aide de caractères ASCII. Évitez d’utiliser des caractères de ponctuation Latin-1 ou Unicode. Ces caractères sont souvent par inadvertance ajoutés lors de la copie de texte à partir d’une application de traitement de texte ou de la capture des données à partir de pages web. Si ces caractères sont présents, veillez à les mettre à jour avec la substitution ASCII appropriée.

Voici quelques exemples :

| Caractères à éviter | Substitution | Notes |
|---------------------|--------------|-------|
| « Hello world » | "Hello world" | Les marques de quotations ouvrantes et fermantes ont été substitués avec des caractères ASCII. |
| Jours de John | Jours de John | L’apostrophe a été remplacé par le caractère ASCII approprié. |
| it was good—no, it was great! | it was good--no, it was great! | Le tiret em a été remplacé par deux traits d’union. |

### <a name="text-normalization-for-us-english"></a>Normalisation du texte anglais (États-Unis)

La normalisation de texte est la transformation de mots dans un format cohérent utilisé lors de l’apprentissage d’un modèle. Certaines règles de normalisation sont automatiquement appliquées au texte, toutefois, nous recommandons à l’aide de ces instructions lorsque vous vous préparez vos données de transcription étiquetés humaines :

* Écrire les abréviations dans les mots.
* Écrire des chaînes numériques non standards dans les mots (tels que les termes du contrat de comptabilité).
* Les caractères non alphabétiques ou des caractères alphanumériques mixtes doivent être transcrite tels prononcés.
* Abréviations sont prononcées en tant que mots ne doit pas être modifiées (par exemple, « en radar », « laser », « RAM » ou « OTAN »).
* Écriture des abréviations soient marquées comme des lettres distinctes avec chaque lettre séparé par un espace.

Voici quelques exemples de normalisation à effectuer sur la transcription :

| Texte d’origine | Texte après la normalisation |
|---------------|--------------------------|
| Dr. Bruce Banner | Docteur Bruce Banner |
| James Bond, 007 | James Bond, double oh seven |
| Ke$ha | Kesha |
| How long is the 2x4 | How long is the two by four |
| The meeting goes from 1-3pm | The meeting goes from one to three pm |
| mon type de sang est O + | My blood type is O positive |
| l’eau est H20 | l’eau est O de 2 H |
| lire OU812 par Van Halen | Lire O, U 8 1 2 par Van Halen |
| UTF-8 with BOM | U T F 8 with BOM |

Les règles de normalisation suivants sont automatiquement appliquées aux transcriptions :

* Utiliser des lettres minuscules.
* Supprimez tous les signes de ponctuation, à l’exception des apostrophes dans les mots.
* Développez des nombres en forme parlée/mots, tels que des montants en dollars.

Voici quelques exemples de normalisation automatique effectuée sur la transcription :

| Texte d’origine | Texte après la normalisation |
|---------------|--------------------------|
| "Holy cow!" said Batman. | holy cow said batman |
| "What?" said Batman’s sidekick, Robin. | what said batman’s sidekick robin |
| Go get -em! | go get em |
| I’m double-jointed | I'm double jointed |
| 104 Elm Street | one oh four Elm street |
| Tune to 102.7 | tune to one oh two seven |
| Pi is about 3.14 | pi is about three point one four |
It costs $3.14| it costs three fourteen |

## <a name="mandarin-chinese-zh-cn"></a>Mandarin chinois (zh-cn)

Humaines en partie les transcriptions audio chinois Mandarin doivent être encodé avec un marqueur d’ordre d’octet UTF-8. Évitez l’utilisation des signes de ponctuation de demi-largeur. Ces caractères peuvent être inclus par inadvertance lorsque vous préparez les données dans un programme de traitement de texte ou capturer des données à partir de pages web. Si ces caractères sont présents, veillez à les mettre à jour avec la substitution de pleine chasse appropriée.

Voici quelques exemples :

| Caractères à éviter | Substitution | Notes |
|---------------------|--------------|-------|
| "你好" | "你好" | Les marques de quotations ouvrantes et fermantes ont été substitués avec des caractères appropriés. |
| 需要什么帮助? | 需要什么帮助？ | Le point d’interrogation a été remplacé par le caractère approprié. |

### <a name="text-normalization-for-mandarin-chinese"></a>Normalisation de texte pour le chinois Mandarin

La normalisation de texte est la transformation de mots dans un format cohérent utilisé lors de l’apprentissage d’un modèle. Certaines règles de normalisation sont automatiquement appliquées au texte, toutefois, nous recommandons à l’aide de ces instructions lorsque vous vous préparez vos données de transcription étiquetés humaines :

* Écrire les abréviations dans les mots.
* Écrivez les chaînes numériques sous leur forme orale.

Voici quelques exemples de normalisation à effectuer sur la transcription :

| Texte d’origine | Texte après la normalisation |
|---------------|--------------------------|
| 我今年21 | 我今年二十一 |
| 3号楼504 | 三号 楼 五 零 四 |

Les règles de normalisation suivants sont automatiquement appliquées aux transcriptions :

* Supprimez tous les signes de ponctuation
* Développez des nombres à forme parlée
* Convertir les lettres pleine chasse demi-chasse
* Utilisation de lettres majuscules pour tous les mots anglais

Voici quelques exemples de normalisation automatique effectuée sur la transcription :

| Texte d’origine | Texte après la normalisation |
|---------------|--------------------------|
| 3.1415 | 三 点 一 四 一 五 |
| ￥3.5 | 三 元 五 角 |
| w f y z |W F Y Z |
| 1992年8月8日 | 一 九 九 二 年 八 月 八 日 |
| 你吃饭了吗? | 你 吃饭 了 吗 |
| 下午5:00的航班 | 下午 五点 的 航班 |
| 我今年21岁 | 我 今年 二十 一 岁 |

## <a name="german-de-de-and-other-languages"></a>Allemand (fr-fr) et autres langages

Transcriptions de légende humaines pour audio allemand (et d’autres non anglaises ou langues chinois Mandarin) doivent être encodé avec un marqueur d’ordre d’octet UTF-8. Une transcription étiquetés humaines doit être fournie pour chaque fichier audio.

### <a name="text-normalization-for-german"></a>Normalisation de texte pour l’allemand

La normalisation de texte est la transformation de mots dans un format cohérent utilisé lors de l’apprentissage d’un modèle. Certaines règles de normalisation sont automatiquement appliquées au texte, toutefois, nous recommandons à l’aide de ces instructions lorsque vous vous préparez vos données de transcription étiquetés humaines :

*   Écrire les décimales en tant que «, « et non ». ».
*   Écrire des séparateurs d’heure en tant que « : « et non ». » (par exemple : 12:00 Uhr).
*   Les abréviations telles que « ca ». ne sont pas remplacées. Nous vous recommandons d’utiliser la forme parlée complète.
*   Les quatre principaux opérateurs mathématiques (+, -, \*, /) sont supprimés. Nous vous recommandons de les remplacer par la forme écrite : « en outre, » « moins, » « mal » et « geteilt ».
*   Opérateurs de comparaison sont supprimés (=, <, et >). Nous vous recommandons de les remplacer par « gleich », « kleiner als » et « grösser als ».
*   Écrire des fractions, tel que 3/4, dans la forme écrite (par exemple : « drei viertel » au lieu de 3/4).
*   Remplacez le symbole « €» avec sa forme écrite « Euro. »

Voici quelques exemples de normalisation à effectuer sur la transcription :

| Texte d’origine | Texte après la normalisation de l’utilisateur | Texte après la normalisation du système |
|---------------|-------------------------------|---------------------------------|
| Es ist 12.23 Uhr | Es ist 12:23 Uhr | es ist zwölf uhr drei und zwanzig uhr |
| {12.45} | {12,45} | zwölf komma vier fünf |
| 2 + 3 - 4 | 2 plus 3 minus 4 | zwei plus drei minus vier |

Les règles de normalisation suivants sont automatiquement appliquées aux transcriptions :

* Utiliser des minuscules pour tout le texte.
* Supprimez tous les signes de ponctuation, y compris les différents types de guillemets (« test », « test », « test » et « test » est OK).
* Ignorer les lignes avec des caractères spéciaux à partir de cet ensemble : ¢ ¤ ¥ l’objectif § © consignes ¬­® ° + ² µ × ÿ Ø¬¬.
* Développez des nombres à forme parlée, y compris dollar ou des montants en euros.
* Accepter des trémas uniquement pour un, o et vous. D’autres seront remplacées par « th » ou être ignorés.

Voici quelques exemples de normalisation automatique effectuée sur la transcription :

| Texte d’origine | Texte après la normalisation |
|---------------|--------------------------|
| Frankfurter Ring | frankfurter ring |
| ¡Eine Frage! | eine frage |
| wir, haben | wir haben |

## <a name="next-steps"></a>Étapes suivantes

* [Préparer et tester vos données](how-to-custom-speech-test-data.md)
* [Inspectez vos données](how-to-custom-speech-inspect-data.md)
* [Évaluer vos données](how-to-custom-speech-evaluate-data.md)
* [Formation d’un modèle](how-to-custom-speech-train-model.md)
* [Déployer votre modèle](how-to-custom-speech-deploy-model.md)
