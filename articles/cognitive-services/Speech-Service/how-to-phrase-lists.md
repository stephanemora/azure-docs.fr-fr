---
title: Liste d’expressions - Service Speech
titleSuffix: Azure Cognitive Services
description: Découvrez comment fournir au service Speech une liste d’expressions à l’aide de l’objet `PhraseListGrammar` pour améliorer les résultats de la reconnaissance vocale.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: trbye
zone_pivot_groups: programming-languages-speech-services-one-nomore-no-go
ms.custom: devx-track-javascript, devx-track-csharp
ms.openlocfilehash: a71b0ba459d9363f6f479fd88ef10c8209eaf47c
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88918721"
---
# <a name="phrase-lists-for-speech-to-text"></a>Listes d’expressions pour la reconnaissance vocale

En fournissant au service Speech une liste d’expressions, vous pouvez améliorer le niveau de justesse de la reconnaissance vocale. Les listes d’expressions permettent d’identifier des expressions connues dans les données audio, comme le nom d’une personne ou un lieu spécifique.

Par exemple, si vous disposez d’une commande « Move to » et que parmi les destinations susceptibles d’être prononcées figurent « Ward », vous pouvez ajouter l’entrée « Move to Ward ». Ainsi, quand le contenu audio est reconnu, l’ajout de cette expression augmente la probabilité que « Move to Ward » sera reconnu et non « Move toward ».

Il est possible d’ajouter des mots uniques ou des expressions entières à une liste d’expressions. Pendant la reconnaissance, une entrée de liste d’expressions est utilisée si une correspondance exacte pour l’expression entière est incluse dans le contenu audio contient sous la forme d’une expression distincte. Si une correspondance exacte avec l’expression est introuvable, la reconnaissance n’est pas assistée.

>[!Note]
> Actuellement, les listes d’expressions ne prennent en charge que l’anglais pour la reconnaissance vocale.

## <a name="how-to-use-phrase-lists"></a>Comment utiliser les listes d’expressions

Les exemples ci-dessous montrent comment créer une liste d’expressions à l’aide de l’objet `PhraseListGrammar`.

::: zone pivot="programming-language-csharp"

```cs
PhraseListGrammar phraseList = PhraseListGrammar.FromRecognizer(recognizer);
phraseList.AddPhrase("Move to Ward");
phraseList.AddPhrase("Move to Bill");
phraseList.AddPhrase("Move to Ted");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
auto phraselist = PhraseListGrammar::FromRecognizer(recognizer);
phraselist->AddPhrase("Move to Ward");
phraselist->AddPhrase("Move to Bill");
phraselist->AddPhrase("Move to Ted");
```

::: zone-end

::: zone pivot="programming-language-java"

```Java
PhraseListGrammar phraseListGrammar = PhraseListGrammar.fromRecognizer(recognizer);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
phrase_list_grammar = speechsdk.PhraseListGrammar.from_recognizer(reco)
phrase_list_grammar.addPhrase("Move to Ward")
phrase_list_grammar.addPhrase("Move to Bill")
phrase_list_grammar.addPhrase("Move to Ted")
```

::: zone-end

::: zone pivot="programming-language-javascript"

```JavaScript
var phraseListGrammar = SpeechSDK.PhraseListGrammar.fromRecognizer(reco);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

::: zone-end

>[!Note]
> Le service Speech peut utiliser au maximum 1 024 listes d’expressions pour la reconnaissance vocale.

Vous pouvez aussi effacer les expressions associées à `PhraseListGrammar` en appelant clear().

::: zone pivot="programming-language-csharp"

```cs
phraseList.Clear();
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
phraselist->Clear();
```

::: zone-end

::: zone pivot="programming-language-java"

```Java
phraseListGrammar.clear();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
phrase_list_grammar.clear()
```

::: zone-end

::: zone pivot="programming-language-javascript"

```JavaScript
phraseListGrammar.clear();
```

::: zone-end

> [!NOTE]
> Les modifications apportées à un objet `PhraseListGrammar` prennent effet lors de la reconnaissance suivante ou après une reconnexion au service Speech.

## <a name="next-steps"></a>Étapes suivantes

* [Documentation de référence du SDK Speech](speech-sdk.md)
