---
title: Paramètres de l’application
titleSuffix: Azure Cognitive Services
description: Comprendre les paramètres de l’application pour les applications de présentation de langage.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/29/2019
ms.author: diberry
ms.openlocfilehash: c19d328c3a5f4dd6dbe14ca94809e42c5655ea72
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66391143"
---
# <a name="application-settings"></a>Paramètres de l’application

Ces paramètres d’application sont stockés dans le [exporté](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) application et [mis à jour](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) avec les API REST. Modification des paramètres de version application rétablit le statut de votre formation application non formé.

|Paramètre|Valeur par défaut|Notes|
|--|--|--|
|NormalizePunctuation|True|Supprime les signes de ponctuation.|
|NormalizeDiacritics|True|Supprime les signes diacritiques.|

## <a name="diacritics-normalization"></a>Normalisation de signes diacritiques 

Activer la normalisation énoncé pour les signes diacritiques à votre fichier d’application LUIS JSON dans le `settings` paramètre.

```JSON
"settings": [
    {"name": "NormalizeDiacritics", "value": "true"}
] 
```

Les énoncés suivants montrent l’impact de la normalisation de signes diacritiques énoncés :

|Avec la valeur false de signes diacritiques|Dont les signes diacritiques est définie sur true|
|--|--|
|`quiero tomar una piña colada`|`quiero tomar una pina colada`|
|||

### <a name="language-support-for-diacritics"></a>Prise en charge linguistique pour les signes diacritiques

#### <a name="brazilian-portuguese-pt-br-diacritics"></a>Portugais (Brésil) `pt-br` les signes diacritiques

|Les signes diacritiques défini sur false|Les signes diacritiques défini sur false|
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

#### <a name="dutch-nl-nl-diacritics"></a>Néerlandais `nl-nl` les signes diacritiques

|Les signes diacritiques défini sur false|Les signes diacritiques défini sur false|
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

#### <a name="french-fr--diacritics"></a>Français `fr-` les signes diacritiques

Cela inclut les sous-cultures français et canadiens.

|Les signes diacritiques défini sur false|Les signes diacritiques défini sur false|
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

#### <a name="german-de-de-diacritics"></a>Allemand `de-de` les signes diacritiques

|Les signes diacritiques défini sur false|Les signes diacritiques défini sur false|
|--|--|
|`ä`|`a`|
|`ö `|`o`| 
|`ü `|`u`| 

#### <a name="italian-it-it-diacritics"></a>Italien `it-it` les signes diacritiques

|Les signes diacritiques défini sur false|Les signes diacritiques défini sur false|
|--|--|
|`à`|`a`|
|`è`|`e`|
|`é`|`e`|
|`ì`|`i`| 
|`í`|`i`| 
|`î`|`i`| 
|`ò`|`o`| 
|`ó `|`o`| 
|`ù`|`u`|
|`ú`|`u`|

#### <a name="spanish-es--diacritics"></a>Espagnol `es-` les signes diacritiques

Cela inclut le Mexique espagnol et canadiens.

|Les signes diacritiques défini sur false|Les signes diacritiques défini sur false|
|-|-|
|`á`|`a`|
|`é`|`e`|
|`í`|`i`| 
|`ó `|`o`| 
|`ú`|`u`|
|`ü`|`u`|
|`ñ`|`u`|


## <a name="punctuation-normalization"></a>Normalisation des signes de ponctuation

Activer la normalisation énoncé d’une ponctuation à votre fichier d’application LUIS JSON dans le `settings` paramètre.

```JSON
"settings": [
    {"name": "NormalizePunctuation", "value": "true"}
] 
```

Les énoncés suivants montrent comment les signes diacritiques a un impact sur énoncés :

|Avec la valeur False de signes diacritiques|Avec la valeur True de signes diacritiques|
|--|--|
|`Hmm..... I will take the cappuccino`|`Hmm I will take the cappuccino`|
|||

### <a name="punctuation-removed"></a>Ponctuation

La ponctuation suivante est supprimée avec `NormalizePunctuation` est définie sur true.

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
