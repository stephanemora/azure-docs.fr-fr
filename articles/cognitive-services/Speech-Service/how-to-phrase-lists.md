---
title: Liste d’expressions – Service Speech
titleSuffix: Azure Cognitive Services
description: Découvrez comment fournir à Speech Services une liste d’expressions à l’aide de l’objet `PhraseListGrammar` pour améliorer les résultats de la reconnaissance vocale.
services: cognitive-services
author: rhurey
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: rhurey
ms.openlocfilehash: 0e552d502184d1b537263c2c1f6b2a8562cdf791
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562783"
---
# <a name="phrase-lists-for-speech-to-text"></a>Listes d’expressions pour la reconnaissance vocale

En fournissant à Speech Services une liste d’expressions, vous pouvez améliorer la précision de la reconnaissance vocale. Les listes d’expressions permettent d’identifier des expressions connues dans les données audio, comme le nom d’une personne ou un lieu spécifique.

Par exemple, si vous disposez d’une commande « Move to » et que parmi les destinations susceptibles d’être prononcées figurent « Ward », vous pouvez ajouter l’entrée « Move to Ward ». Ainsi, quand le contenu audio est reconnu, l’ajout de cette expression augmente la probabilité que « Move to Ward » sera reconnu et non « Move toward ».

Il est possible d’ajouter des mots uniques ou des expressions entières à une liste d’expressions. Pendant la reconnaissance, une entrée de liste d’expressions est utilisée si le contenu audio contient une correspondance exacte. Pour reprendre l’exemple précédent, si la liste d’expressions contient « Move to Ward » et que l’expression détectée est « Move toward slowly », le résultat de la reconnaissance sera « Move to Ward slowly ».

>[!Note]
> Actuellement, les listes d’expressions ne prennent en charge que l’anglais pour la reconnaissance vocale.

## <a name="how-to-use-phrase-lists"></a>Comment utiliser les listes d’expressions

Les exemples ci-dessous montrent comment créer une liste d’expressions à l’aide de l’objet `PhraseListGrammar`.

```C++
auto phraselist = PhraseListGrammar::FromRecognizer(recognizer);
phraselist->AddPhrase("Move to Ward");
phraselist->AddPhrase("Move to Bill");
phraselist->AddPhrase("Move to Ted");
```

```cs
PhraseListGrammar phraseList = PhraseListGrammar.FromRecognizer(recognizer);
phraseList.AddPhrase("Move to Ward");
phraseList.AddPhrase("Move to Bill");
phraseList.AddPhrase("Move to Ted");
```

```Python
phrase_list_grammar = speechsdk.PhraseListGrammar.from_recognizer(reco)
phrase_list_grammar.addPhrase("Move to Ward")
phrase_list_grammar.addPhrase("Move to Bill")
phrase_list_grammar.addPhrase("Move to Ted")
```

```JavaScript
var phraseListGrammar = SpeechSDK.PhraseListGrammar.fromRecognizer(reco);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

```Java
PhraseListGrammar phraseListGrammar = PhraseListGrammar.fromRecognizer(recognizer);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

>[!Note]
> Speech Services peut utiliser au maximum 1 024 listes d’expressions pour la reconnaissance vocale.

Vous pouvez aussi effacer les expressions associées à `PhraseListGrammar` en appelant clear().

```C++
phraselist->Clear();
```

```cs
phraseList.Clear();
```

```Python
phrase_list_grammar.clear()
```

```JavaScript
phraseListGrammar.clear();
```

```Java
phraseListGrammar.clear();
```

> [!NOTE]
> Les modifications apportées à un objet `PhraseListGrammar` prennent effet à la reconnaissance suivante ou après une reconnexion à Speech Services.

## <a name="next-steps"></a>Étapes suivantes

* [Documentation de référence du SDK Speech](speech-sdk.md)
