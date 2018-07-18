---
title: API de traduction de texte Microsoft Translator Text - Migration vers la V3 | Microsoft Docs
description: Découvrez comment migrer l’API de traduction de texte Translator Text de la V2 à la V3.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: translator
ms.topic: article
ms.date: 03/27/2018
ms.author: v-jansko
ms.openlocfilehash: 16fec351af5b5b3875657ee244c18f305311d965
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35370244"
---
# <a name="microsoft-translator-text-api-v2-to-v3-migration"></a>Migration de l’API de traduction de texte Microsoft Translator Text de la V2 à la V3

L’équipe Microsoft Translator a publié la version 3 (V3) de l’API de traduction de texte. Cette version inclut de nouvelles fonctionnalités, les méthodes déconseillées et un nouveau format pour l’envoi et la réception de données à partir du service de traduction de Microsoft. Ce document fournit des informations sur la modification des applications pour utiliser la V3. La V2 deviendra déconseillée à partir du 30 avril 2018 et sera supprimée le 30 avril 2019.

La fin de ce document contient des liens utiles pour vous permettre d’en savoir plus.

## <a name="summary-of-features"></a>Résumé des fonctionnalités

* Sans trace - L’approche Sans trace s’applique à tous les niveaux tarifaires du portail Azure dans la V3. Cela signifie qu’aucun texte soumis à l’API V3 ne sera enregistré par Microsoft.
* JSON - XML est remplacé par JSON. Toutes les données envoyées au service et reçues à partir du service sont au format JSON.
* Plusieurs langues cible dans une requête unique - La méthode Translate accepte plusieurs langues cible pour la traduction dans une requête unique. Par exemple, une requête unique peut être de l’anglais vers l’allemand, l’espagnol et le japonais ou tout autre groupe de langues.
* Bilingual dictionary - Une méthode Bilingual dictionary a été ajoutée à l’API. Cette méthode inclut la « recherche » et les « exemples ».
* Transliterate - Une méthode Transliterate a été ajoutée à l’API. Cette méthode convertit les mots ou les phrases d’un script (par exemple : arabe) dans un autre script (par exemple : latin).
* Languages - Une nouvelle méthode « Languages » donne des informations linguistiques au format JSON, à utiliser avec les méthodes Translate, Dictionary et Transliterate.
* Nouveautés pour la méthode Translate - De nouvelles fonctionnalités ont été ajoutées à la méthode « Translate » pour prendre en charge certaines des fonctionnalités qui étaient auparavant des méthodes distinctes dans l’API V2. Par exemple, TranslateArray.
* Méthode Speak - La fonctionnalité de synthèse vocale n’est plus prise en charge dans l’API Microsoft Translator. La fonctionnalité de synthèse vocale est disponible dans l’API Reconnaissance vocale Bing Microsoft Azure Cognitive Services.

La liste suivante des méthodes V2 et V3 identifie les méthodes V3 et les API qui fourniront les fonctionnalités de la V2.

| Méthode API V2   | Compatibilité API V3 |
|:----------- |:-------------|
| Translate     | Translate          |
| TranslateArray      | Translate          |
| GetLanguageNames      | Langues          |
| GetLanguagesForTranslate     | Langues        |
| GetLanguagesForSpeak      | API Speech Cognitive Services         |
| Speak     | API Speech Cognitive Services          |
| Détection     | Détection         |
| DetectArray     | Détection         |
| AddTranslation     | API Hub Microsoft Translator         |
| AddTranslationArray    | API Hub Microsoft Translator          |
| BreakSentences      | BreakSentence         |
| GetTranslations      | Cette fonctionnalité n’est plus prise en charge         |
| GetTranslationsArray      | Cette fonctionnalité n’est plus prise en charge         |

## <a name="move-to-json-format"></a>Passer au format JSON

L’API de traduction de texte Microsoft Translator Text V2 acceptait et renvoyait les données au format XML. Dans la V3, toutes les données envoyées et reçues à l’aide de l’API sont au format JSON. XML n’est plus accepté ou renvoyé dans la V3. 

Cette modification affecte plusieurs aspects d’une application écrite pour l’API de traduction de texte V2. Par exemple : l’API Langues renvoie des informations linguistiques pour la traduction de texte, la translittération et les deux méthodes de dictionnaire. Vous pouvez demander toutes les informations linguistiques pour toutes les méthodes dans un seul appel ou les demander individuellement.

La méthode Languages ne nécessite pas d’authentification ; en cliquant sur le lien suivant, vous pouvez voir toutes les informations linguistiques pour la V3 dans JSON :

[https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation,dictionary,transliteration](https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation,dictionary,transliteration)

## <a name="authentication-key"></a>Clé d’authentification

La clé d’authentification que vous utilisez pour la V2 sera acceptée pour la V3. Vous n’aurez pas besoin d’obtenir un nouvel abonnement. Vous serez en mesure de mélanger la V2 et la V3 dans vos applications pendant la période de migration d’un an, ce qui vous permettra de publier de nouvelles versions pendant la migration de la V2 (XML) à la V3 (JSON).

## <a name="pricing-model"></a>Modèle de tarification

La tarification de Microsoft Translator V3 est la même que celle de la V2 ; par caractère, espaces compris. Les nouvelles fonctionnalités de la V3 apportent des modifications dans les caractères comptabilisés pour la facturation.

| Méthode V3   | Caractères comptabilisés pour la facturation |
|:----------- |:-------------|
| Langues     | Aucun caractère envoyé, aucun comptabilisé, aucuns frais.          |
| Translate     | La comptabilisation se base sur le nombre de caractères envoyés pour traduction et dans combien de langues ils doivent être traduits. 50 caractères envoyés, 5 langues demandées, cela fait 50 x 5.           |
| Transliterate     | Le nombre de caractères envoyés pour translittération est comptabilisé.         |
| Recherche dans le dictionnaire et exemple     | Le nombre de caractères envoyés pour la recherche dans le dictionnaire et les exemples est comptabilisé.         |
| BreakSentence     | Aucuns frais.       |
| Détection     | Aucuns frais.      |

## <a name="v3-end-points"></a>Points de terminaison V3

Globale

* api.cognitive.microsofttranslator.com


## <a name="v3-api-text-translations-methods"></a>Méthodes de traduction de texte de l’API V3

[Langues](reference/v3-0-languages.md)

[Translate](reference/v3-0-translate.md)

[Transliterate](reference/v3-0-transliterate.md)

[BreakSentence](reference/v3-0-break-sentence.md)

[Detect](reference/v3-0-detect.md)

[Dictionary/lookup](reference/v3-0-dictionary-lookup.md)

[Dictionary/example](reference/v3-0-dictionary-examples.md)

## <a name="customization"></a>Personnalisation

Microsoft Translator V3 utilise par défaut la traduction automatique neuronale. Par conséquent, il ne peut pas être utilisé avec le hub Microsoft Translator, qui prend uniquement en charge la traduction automatique statistique héritée. La personnalisation de la traduction neuronale est désormais disponible à l’aide du traducteur personnalisé. [En savoir plus sur la personnalisation de la traduction automatique neuronale](customization.md)

La traduction neuronale avec l’API de texte V3 ne prend pas en charge l’utilisation des catégories standard (smt, speech, text, generalnn).


## <a name="links"></a>Liens

* [Politique de confidentialité Microsoft](https://privacy.microsoft.com/privacystatement)
* [Informations légales relatives à Microsoft Azure](https://azure.microsoft.com/support/legal)
* [Conditions d’utilisation des services en ligne](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Consulter la documentation de la V3.0](reference/v3-0-reference.md)
