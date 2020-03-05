---
title: Paramètres d’application - LUIS
titleSuffix: Azure Cognitive Services
description: Les paramètres d’application pour des applications Language Understanding Azure Cognitive Services sont stockés dans l’application et le portail.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 11/12/2019
ms.author: diberry
ms.openlocfilehash: d1ead09f6248a6ad14646371aa70b42b57cf8e3f
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2020
ms.locfileid: "78270816"
---
# <a name="application-settings"></a>Paramètres de l’application

Ces paramètres d’application sont stockés dans l’application [exportée](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) et [mis à jour](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) avec les API REST. Le fait de modifier vos paramètres de version d’application réinitialise le statut d’entraînement de votre application sur « Non entraînée ».

|Paramètre|Valeur par défaut|Notes|
|--|--|--|
|NormalizePunctuation|True|Supprime les signes de ponctuation.|
|NormalizeDiacritics|True|Supprime les signes diacritiques.|

## <a name="diacritics-normalization"></a>Normalisation des signes diacritiques

Activez la normalisation de l’énoncé pour les signes diacritiques dans votre fichier d’application LUIS JSON avec le paramètre `settings`.

```JSON
"settings": [
    {"name": "NormalizeDiacritics", "value": "true"}
]
```

Les énoncés suivants montrent l’impact de la normalisation des signes diacritiques sur les énoncés :

|Avec les signes diacritiques définis sur false|Avec les signes diacritiques définis sur true|
|--|--|
|`quiero tomar una piña colada`|`quiero tomar una pina colada`|
|||

### <a name="language-support-for-diacritics"></a>Prise en charge linguistique pour les signes diacritiques

#### <a name="brazilian-portuguese-pt-br-diacritics"></a>Signes diacritiques pour le Portugais (Brésil) `pt-br`

|Signes diacritiques définis sur false|Signes diacritiques définis sur true|
|-|-|
|`á`|`a`|
|`â`|`a`|
|`ã`|`a`|
|`à`|`a`|
|`ç`|`c`|
|`é`|`e`|
|`ê`|`e`|
|`í`|`i`|
|`ó`|`o`|
|`ô`|`o`|
|`õ`|`o`|
|`ú`|`u`|
|||

#### <a name="dutch-nl-nl-diacritics"></a>Signes diacritiques pour le Néerlandais `nl-nl`

|Signes diacritiques définis sur false|Signes diacritiques définis sur true|
|-|-|
|`á`|`a`|
|`à`|`a`|
|`é`|`e`|
|`ë`|`e`|
|`è`|`e`|
|`ï`|`i`|
|`í`|`i`|
|`ó`|`o`|
|`ö`|`o`|
|`ú`|`u`|
|`ü`|`u`|
|||

#### <a name="french-fr--diacritics"></a>Signes diacritiques pour le Français `fr-`

Cela inclut les sous-cultures Français et Canadien.

|Signes diacritiques définis sur false|Signes diacritiques définis sur true|
|--|--|
|`é`|`e`|
|`à`|`a`|
|`è`|`e`|
|`ù`|`u`|
|`â`|`a`|
|`ê`|`e`|
|`î`|`i`|
|`ô`|`o`|
|`û`|`u`|
|`ç`|`c`|
|`ë`|`e`|
|`ï`|`i`|
|`ü`|`u`|
|`ÿ`|`y`|

#### <a name="german-de-de-diacritics"></a>Signes diacritiques pour l’Allemand `de-de`

|Signes diacritiques définis sur false|Signes diacritiques définis sur true|
|--|--|
|`ä`|`a`|
|`ö`|`o`|
|`ü`|`u`|

#### <a name="italian-it-it-diacritics"></a>Signes diacritiques pour l’Italien `it-it`

|Signes diacritiques définis sur false|Signes diacritiques définis sur true|
|--|--|
|`à`|`a`|
|`è`|`e`|
|`é`|`e`|
|`ì`|`i`|
|`í`|`i`|
|`î`|`i`|
|`ò`|`o`|
|`ó`|`o`|
|`ù`|`u`|
|`ú`|`u`|

#### <a name="spanish-es--diacritics"></a>Signes diacritiques pour l’Espagnol `es-`

Cela inclut les sous-cultures Mexicain et Espagnol.

|Signes diacritiques définis sur false|Signes diacritiques définis sur true|
|-|-|
|`á`|`a`|
|`é`|`e`|
|`í`|`i`|
|`ó`|`o`|
|`ú`|`u`|
|`ü`|`u`|
|`ñ`|`u`|


## <a name="punctuation-normalization"></a>Normalisation des signes de ponctuation

Activez la normalisation de l’énoncé pour la ponctuation dans votre fichier d’application LUIS JSON avec le paramètre `settings`.

```JSON
"settings": [
    {"name": "NormalizePunctuation", "value": "true"}
]
```

Les énoncés suivants montrent l’impact de la ponctuation sur les énoncés :

|Avec la ponctuation définie sur False|Avec la ponctuation définie sur True|
|--|--|
|`Hmm..... I will take the cappuccino`|`Hmm I will take the cappuccino`|
|||

### <a name="punctuation-removed"></a>Ponctuation supprimée

La ponctuation suivante est supprimée quand `NormalizePunctuation` a la valeur true.

|Ponctuation|
|--|
|`-`|
|`.`|
|`'`|
|`"`|
|`\`|
|`/`|
|`?`|
|`!`|
|`_`|
|`,`|
|`;`|
|`:`|
|`(`|
|`)`|
|`[`|
|`]`|
|`{`|
|`}`|
|`+`|
|`¡`|
