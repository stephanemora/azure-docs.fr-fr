---
title: Instructions concernant la transcription dans Custom Speech Service sur Azure | Microsoft Docs
description: Apprenez à préparer vos données pour Custom Speech Service dans Cognitive Services.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/08/2017
ms.author: panosper
ms.openlocfilehash: 2785a35ac7583ac3d9503cb721d10078d86aa365
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35368141"
---
# <a name="transcription-guidelines"></a>Instructions concernant la transcription
Pour garantir la meilleure utilisation de vos données de texte pour la personnalisation de modèles de langage et acoustiques, suivez les instructions sur la transcription suivantes. Ces instructions sont spécifiques à une langue.

## <a name="text-normalization"></a>Normalisation du texte

Pour une utilisation optimale de la personnalisation de modèles de langage ou acoustiques, les données de texte doivent être normalisées. Elles doivent donc être transformées en une forme standard et sans ambiguïté pouvant être lue par le système. Cette section décrit la normalisation du texte réalisée par Custom Speech Service lorsque des données sont importées, et celle que l’utilisateur doit effectuer avant l’importation de ces données.

## <a name="inverse-text-normalization"></a>Normalisation du texte inversée

Le processus de conversion de texte « brut » non formaté en texte formaté (avec majuscules et ponctuation, par exemple), est appelé normalisation du texte inversée (ITN). Cette normalisation est effectuée sur les résultats retournés par l’API Microsoft Speech de Microsoft Cognitive Services. Un point de terminaison personnalisé déployé avec Custom Speech Service utilise la même normalisation inversée que l’API Microsoft Speech de Microsoft Cognitive Services. Toutefois, ce service ne prend actuellement pas en charge la normalisation de texte inversée personnalisée. Les nouveaux termes introduits par un modèle de langage personnalisé ne seront donc pas formatés dans les résultats de reconnaissance.

## <a name="transcription-guidelines-for-en-us"></a>Instructions concernant la transcription pour fr-FR

Les données de texte téléchargées vers ce service doivent être écrites en texte brut en utilisant uniquement le jeu de caractères imprimables ASCII. Chaque ligne du fichier doit contenir le texte d’un seul énoncé.

Il est important d’éviter l’utilisation des signes de ponctuation Unicode. Cela peut arriver par inadvertance lors de la préparation des données dans un programme de traitement de texte ou lors de la capture de données à partir de pages web. Remplacez ces caractères par des substituts ASCII appropriés. Par exemple : 

| Unicode à éviter | Substitution ASCII |
|----- | ----- |
| “Hello world” (guillemets doubles ouvrants et fermants) | "Hello world" (guillemets doubles droits) |
| John’s day (apostrophe courbe) | John's day (apostrophe droite) |

### <a name="text-normalization-performed-by-the-custom-speech-service"></a>Normalisation de texte effectuée par Custom Speech Service

Ce service effectue la normalisation de texte suivante sur les données importées comme ensemble de données de langage ou transcriptions pour un ensemble de données acoustiques. Elle inclut ce qui suit

*   Mise en minuscules de l’ensemble du texte
*   Suppression de tous les signes de ponctuation, à l’exception des apostrophes à l’intérieur des mots
*   Développement des nombres en forme orale, y compris les montants en dollars

Voici quelques exemples

| Texte d’origine | Après la normalisation |
|----- | ----- |
| Starbucks Coffee | starbucks coffee |
| “Holy cow!” said Batman. | holy cow said batman |
| “What?” said Batman's sidekick, Robin. | what said batman's sidekick robin |
| Go get -em! | go get em |
| i'm double jointed | i'm double jointed |
| 104 Main Street | one oh four main street |
| Tune to 102.7 | tune to one oh two point seven |
| Pi is about 3.14 | pi is about three point one four |
| It costs $3.14 | it costs three fourteen |

### <a name="text-normalization-required-by-users"></a>Normalisation du texte requise par les utilisateurs

Pour garantir la meilleure utilisation de vos données, les règles de normalisation suivantes doivent être appliquées à vos données avant leur importation.

*   Les abréviations doivent être développées afin de refléter la forme orale
*   Les chaînes numériques non standard doivent être développées en mots
*   Les mots contenant des caractères non alphabétiques ou des caractères alphanumériques mixtes doivent être retranscrits conformément à leur prononciation
*   Les acronymes communs peuvent être laissés en entité unique sans virgule ou espace entre les lettres, mais tous les autres acronymes doivent être écrits avec des lettres séparées par un seul espace.

Voici quelques exemples

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

## <a name="transcription-guidelines-for-zh-cn"></a>Instructions concernant la transcription pour zh-CN

Les données de texte téléchargées vers le service Custom Speech Service doivent utiliser l’**encodage UTF-8 (avec BOM)**. Chaque ligne du fichier doit contenir le texte d’un seul énoncé.

Il est important d’éviter l’utilisation des signes de ponctuation de demi-largeur. Cela peut arriver par inadvertance lors de la préparation des données dans un programme de traitement de texte ou lors de la capture de données à partir de pages web. Remplacez ces caractères par des substituts appropriés de pleine largeur. Par exemple : 

| Unicode à éviter | Substitution ASCII |
|----- | ----- |
| “你好” (guillemets doubles ouvrants et fermants) | "你好" (guillemets doubles) |
| 需要什么帮助? (point d’interrogation) | 需要什么帮助？ |

### <a name="text-normalization-performed-by-the-custom-speech-service"></a>Normalisation de texte effectuée par Custom Speech Service

Ce service de reconnaissance effectue la normalisation de texte suivante sur les données importées comme ensemble de données de langage ou transcriptions pour un ensemble de données acoustiques. Elle inclut ce qui suit

*   Suppression de tous les signes de ponctuation
*   Développement des nombres vers la forme orale
*   Conversion des lettres pleine largeur en lettres de demi-largeur
*   Mise en majuscules de tous les mots anglais

Voici quelques exemples :

| Texte d’origine | Après la normalisation |
|----- | ----- |
| 3.1415 | 三 点 一 四 一 五 |
| ￥3.5 | 三 元 五 角 |
| w f y z | W F Y Z |
| 1992年8月8日 | 一 九 九 二 年 八 月 八 日 |
| 你吃饭了吗 ? | 你 吃饭 了 吗 |
| 下午5:00的航班 | 下午 五点 的 航班 |
| 我今年21岁 | 我 今年 二十 一 岁 |

### <a name="text-normalization-required-by-users"></a>Normalisation du texte requise par les utilisateurs

Pour garantir la meilleure utilisation de vos données, les règles de normalisation suivantes doivent être appliquées à vos données _avant_ leur importation.

*   Les abréviations doivent être développées afin de refléter la forme orale
*   Ce service ne couvre pas toutes les quantités numériques. Il est plus sûr d’écrire les chaînes numériques sous leur forme orale.

Voici quelques exemples

| Texte d’origine | Après la normalisation |
|----- | ----- |
| 我今年21 | 我今年二十一 |
| 3号楼504 | 三号 楼 五 零 四 |

## <a name="transcription-guidelines-for-de-de"></a>Instructions concernant la transcription pour de-DE

Les données de texte téléchargées vers le service Custom Speech Service ne doivent utiliser que l’**encodage UTF-8 (avec BOM)**. Chaque ligne du fichier doit contenir le texte d’un seul énoncé.

### <a name="text-normalization-performed-by-the-custom-speech-service"></a>Normalisation de texte effectuée par Custom Speech Service

Ce service effectue la normalisation de texte suivante sur les données importées comme ensemble de données de langage ou transcriptions pour un ensemble de données acoustiques. Elle inclut ce qui suit

*   Mise en minuscules de l’ensemble du texte
*   Suppression de tous les signes de ponctuation, y compris les différents types de guillemets ("test", 'test', “test„ ou «test» sont acceptés)
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


### <a name="text-normalization-required-by-users"></a>Normalisation du texte requise par les utilisateurs

Pour garantir la meilleure utilisation de vos données, les règles de normalisation suivantes doivent être appliquées à vos données avant leur importation.

*   Le séparateur décimal doit être une virgule et non un point Par exemple, 2,3 % et non 2.3 %
*   Le séparateur entre les heures et minutes doit être « : » et non « . » : 12:00 Uhr
*   Les abréviations telles que « ca. », « bzw. » ne sont pas remplacées. Nous vous recommandons d’utiliser la forme complète afin d’obtenir la bonne prononciation.
*   Les cinq principaux opérateurs mathématiques sont supprimés : +, -, \*, /.
 Nous vous recommandons de les remplacer par leur forme littérale : plus, minus, mal, geteilt.
*   La même règle s’applique aux comparateurs (=, <, >) - gleich, kleiner als, grösser als
*   Utilisez la forme textuelle des fractions (par exemple, « drei viertel » au lieu de ¾)
*   Remplacez le symbole € par la forme textuelle « Euro »


Voici quelques exemples

| Texte d’origine | Après la normalisation par l’utilisateur | Après la normalisation par le système
|--------  | ----- | -------- |
| Es ist 12.23Uhr | Es ist 12:23Uhr | es ist zwölf uhr drei und zwanzig uhr |
| {12.45} | {12,45} | zwölf komma vier fünf |
| 3 < 5 | 3 kleiner als 5 | drei kleiner als vier |
| 2 + 3 - 4 | 2 plus 3 minus 4 | zwei plus drei minus vier|
| Das macht 12€ | Das macht 12 Euros | das macht zwölf euros |



## <a name="next-steps"></a>Étapes suivantes
* [Comment utiliser un point de terminaison de reconnaissance vocale personnalisé](cognitive-services-custom-speech-create-endpoint.md)
* Améliorer la précision avec votre [modèle acoustique personnalisé](cognitive-services-custom-speech-create-acoustic-model.md)
* Améliorer la précision avec un [modèle de langage personnalisé](cognitive-services-custom-speech-create-language-model.md)
