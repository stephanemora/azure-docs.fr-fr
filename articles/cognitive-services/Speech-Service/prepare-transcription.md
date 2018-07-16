---
title: Instructions concernant la transcription pour l’apprentissage de la reconnaissance vocale | Microsoft Docs
description: Apprenez à préparer le texte pour personnaliser les modèles linguistiques et acoustiques, ainsi que les polices de voix pour le service Speech.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 93ab7c81a773f692b2b970bb1901d82b7aceb5a2
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/05/2018
ms.locfileid: "35370985"
---
# <a name="transcription-guidelines-for-using-speech-service"></a>Instructions concernant la transcription pour l’utilisation du service Speech

Pour personnaliser la **reconnaissance vocale** ou la **synthèse vocale**, vous devez fournir le texte en même temps que la voix. Chaque ligne du texte correspond à un énoncé unique. Le texte est appelé une *transcription* et doit être créé dans un format spécifique.

Le service Speech effectue certaines actions de normalisation afin de rendre votre texte homogène. D’autres tâches de normalisation doivent être effectuées avant que le texte ne soit envoyé pour apprentissage. 

Cet article décrit les deux types de normalisations. Les instructions varient légèrement d’une langue à l’autre.

## <a name="us-english-en-us"></a>Anglais (en-US)

Les données de texte téléchargées vers ce service doivent être écrites en texte brut en utilisant uniquement le jeu de caractères ASCII. Chaque ligne du fichier doit contenir le texte d’un seul énoncé.

Il est important d’éviter l’utilisation des signes de ponctuation étendus (Latin-1) ou Unicode. Ces caractères peuvent être inclus par inadvertance lors de la préparation des données dans un programme de traitement de texte ou lors de la capture de données à partir de pages web. Remplacez ces caractères par des substituts ASCII appropriés. Par exemple : 

| Caractères à éviter | Substitution |
|----- | ----- |
| “Hello world” (guillemets doubles ouvrants et fermants) | "Hello world" (guillemets doubles droits) |
| John’s day (apostrophe courbe) | John’s day (apostrophe) |
| it was good—no, it was great! (tiret long) | it was good--no, it was great! (traits d’union) |

### <a name="text-normalization-performed-by-the-service"></a>Normalisation de texte effectuée par le service

Le service Speech effectue la normalisation de texte suivante sur les transcriptions de texte.

*   Mise en minuscules de l’ensemble du texte
*   Suppression de tous les signes de ponctuation, à l’exception des apostrophes à l’intérieur des mots
*   Développement des nombres en forme orale, y compris les montants en dollars

Voici quelques exemples

| Texte d’origine | Après la normalisation |
|----- | ----- |
| Starbucks Coffee | starbucks coffee |
| "Holy cow!" said Batman. | holy cow said batman |
| "What?" said Batman’s sidekick, Robin. | what said batman’s sidekick robin |
| Go get -em! | go get em |
| I’m double-jointed | i’m double jointed |
| 104 Main Street | one oh four main street |
| Tune to 102.7 | tune to one oh two point seven |
| Pi is about 3.14 | pi is about three point one four |
| It costs $3.14 | it costs three fourteen |

### <a name="text-normalization-you-must-perform"></a>Normalisation de texte que vous devez effectuer

Appliquez la normalisation suivante à vos transcriptions de texte.

*   Les abréviations doivent être développées afin de refléter la forme orale
*   Les chaînes numériques non standard (par exemple, certaines dates ou les formules de comptabilité) doivent être écrites en mots
*   Les mots contenant des caractères non alphabétiques ou des caractères alphanumériques mixtes doivent être retranscrits conformément à leur prononciation
*   Laissez les abréviations prononcées comme des mots inchangées. Par exemple, radar, laser, RAM, NATO et Mr.
*   Écrivez les abréviations prononcées comme des lettres distinctes sous forme de lettres séparées par des espaces. Par exemple, IBM, CPU, FBI, TBD, NaN. 

Voici quelques exemples :

| Texte d’origine | Après la normalisation |
|----- | ----- |
| 14 NE 3rd Dr. | fourteen northeast third drive |
| Dr. Strangelove | Doctor Strangelove |
| James Bond 007 | James Bond double oh seven |
| Ke$ha | Kesha |
| How long is the 2x4 | How long is the two by four |
| The meeting goes from 1-3pm | The meeting goes from one to three pm |
| my blood type is O+ | My blood type is O positive |
| water is H20 | water is H 2 O |
| play OU812 by Van Halen | play O U 8 1 2 by Van Halen |
| UTF-8 with BOM | U T F 8 with BOM |

## <a name="chinese-zh-cn"></a>Chinois (zh-CN)

Les données de texte téléchargées vers le service Custom Speech Service doivent utiliser l’encodage UTF-8 avec un marqueur d’ordre d’octet. Chaque ligne du fichier doit contenir le texte d’un seul énoncé.

Il est important d’éviter l’utilisation des signes de ponctuation de demi-largeur. Ces caractères peuvent être inclus par inadvertance lors de la préparation des données dans un programme de traitement de texte ou lors de la capture de données à partir de pages web. Remplacez-les par les substituts appropriés de largeur normale. Par exemple : 

| Caractères à éviter | Substitution |
|----- | ----- |
| "你好" (guillemets doubles ouvrants et fermants) | "你好" (guillemets doubles) |
| 需要什么帮助? (point d’interrogation) | 需要什么帮助？ |

### <a name="text-normalization-performed-by-the-service"></a>Normalisation de texte effectuée par le service

Le service Speech effectue la normalisation de texte suivante sur les transcriptions de texte.

*   Suppression de tous les signes de ponctuation
*   Développement des nombres vers la forme orale
*   Conversion des lettres pleine largeur en lettres de demi-largeur
*   Mise en majuscules de tous les mots anglais

Voici quelques exemples.

| Texte d’origine | Après la normalisation |
|----- | ----- |
| 3.1415 | 三 点 一 四 一 五 |
| ￥3.5 | 三 元 五 角 |
| w f y z | W F Y Z |
| 1992年8月8日 | 一 九 九 二 年 八 月 八 日 |
| 你吃饭了吗 ? | 你 吃饭 了 吗 |
| 下午5:00的航班 | 下午 五点 的 航班 |
| 我今年21岁 | 我 今年 二十 一 岁 |

### <a name="text-normalization-you-must-perform"></a>Normalisation de texte que vous devez effectuer

Appliquez la normalisation suivante à votre texte avant de l’importer.

*   Les abréviations doivent être développées afin de refléter la forme orale
*   Ce service ne couvre pas toutes les quantités numériques. Il est plus sûr d’écrire les chaînes numériques sous leur forme orale.

Voici quelques exemples.

| Texte d’origine | Après la normalisation |
|----- | ----- |
| 我今年21 | 我今年二十一 |
| 3号楼504 | 三号 楼 五 零 四 |

## <a name="other-languages"></a>Autres langages

Les données de texte téléchargées vers le service de **reconnaissance vocale** doivent utiliser l’encodage UTF-8 avec un marqueur d’ordre d’octet. Chaque ligne du fichier doit contenir le texte d’un seul énoncé.

> [!NOTE]
> Les exemples suivants sont en allemand. Toutefois, ces instructions s’appliquent à toutes les langues autres que l’anglais US ou le chinois.

### <a name="text-normalization-performed-by-the-service"></a>Normalisation de texte effectuée par le service

Le service Speech effectue la normalisation de texte suivante sur les transcriptions de texte.

*   Mise en minuscules de l’ensemble du texte
*   Suppression de tous les signes de ponctuation, y compris les différents types de guillemets ("test", 'test', "test„ ou « test » sont acceptés)
*   Suppression de toutes les lignes contenant l’un des caractères spéciaux suivants ^ ¢ £ ¤ ¥ ¦ § © ª ¬ ® ° ± ² µ × ÿ Ø¬¬
*   Développement des nombres en forme textuelle, y compris les montants en dollars ou en euros
*   Les trémas sont acceptés uniquement sur les lettres a, o et u ; les autres seront remplacés par « th » ou ignorés

Voici quelques exemples

| Texte d’origine | Après la normalisation |
|----- | ----- |
| Frankfurter Ring | frankfurter ring |
| "Hallo, Mama!" sagt die Tochter. | hallo mama sagt die tochter |
| ¡Eine Frage! | eine frage |
| wir, haben | wir haben |
| Das macht $10 | das macht zehn dollars |

### <a name="text-normalization-you-must-perform"></a>Normalisation de texte que vous devez effectuer

Appliquez la normalisation suivante à votre texte avant de l’importer.

*   Le séparateur décimal doit être une virgule et non un point : 2,3 % et non 2.3 %
*   Le séparateur entre les heures et minutes doit être « : » et non « . » : 12:00 Uhr
*   Les abréviations telles que « ca. », « bzw. » ne sont pas remplacées. Nous vous recommandons d’utiliser la forme complète.
*   Les cinq principaux opérateurs mathématiques sont supprimés : +, -, \*, /. Nous vous recommandons de les remplacer par leur forme littérale : plus, minus, mal, geteilt.
*   La même règle s’applique aux opérateurs de comparaison (=, <, >) - gleich, kleiner als, grösser als
*   Utilisez la forme textuelle des fractions (par exemple, « drei viertel » au lieu de ¾)
*   Remplacez le symbole € par la forme textuelle « Euro »

Voici quelques exemples.

| Texte d’origine | Après la normalisation par l’utilisateur | Après la normalisation par le système
|--------  | ----- | -------- |
| Es ist 12.23Uhr | Es ist 12:23Uhr | es ist zwölf uhr drei und zwanzig uhr |
| {12.45} | {12,45} | zwölf komma vier fünf |
| 3 < 5 | 3 kleiner als 5 | drei kleiner als vier |
| 2 + 3 - 4 | 2 plus 3 minus 4 | zwei plus drei minus vier|
| Das macht 12€ | Das macht 12 Euros | das macht zwölf euros |

## <a name="next-steps"></a>Étapes suivantes

- [Obtenir votre abonnement d’essai gratuit du service Speech](https://azure.microsoft.com/try/cognitive-services/)
- [Reconnaissance vocale dans C#](quickstart-csharp-windows.md)
