---
title: Une phrase listes - Services de reconnaissance vocale
titlesuffix: Azure Cognitive Services
description: Découvrez comment fournir les Services de reconnaissance vocale avec une liste d’expressions à l’aide de la `PhraseListGrammar` objet afin d’améliorer les résultats de reconnaissance de la parole-texte.
services: cognitive-services
author: rhurey
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 5/02/2019
ms.author: rhurey
ms.openlocfilehash: a3be5d28ebe394771a2d8b492f1f6a9c8a82fb9e
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66515307"
---
# <a name="phrase-lists-for-speech-to-text"></a>Listes d’une expression pour la parole-texte

En fournissant les Services de reconnaissance vocale avec une liste d’expressions, vous pouvez améliorer la précision de la reconnaissance vocale. Listes d’expression sont utilisés pour identifier les expressions connues dans les données audio, tels que le nom d’une personne ou un emplacement spécifique.

Par exemple, si vous disposez d’une commande « Atteindre » et la destination possible « Ward » qui peut être prononcé, vous pouvez ajouter une entrée de « Déplacer vers Ward ». Ajout d’une expression augmente la probabilité que lorsque l’audio est reconnu que « Déplacer vers Ward » est reconnu au lieu de « Déplacer vers ».

Mots ou expressions complètes peuvent être ajoutées à une liste d’expressions. Lors de la reconnaissance, une entrée dans une liste d’expressions est utilisée si une correspondance exacte n’est incluse dans le fichier audio. Création sur l’exemple précédent, si la liste d’expressions inclut « Déplacer vers Ward » et l’expression capturé est « Déplacer lentement vers », puis le résultat de reconnaissance sera « Déplacer lentement à Ward ».

## <a name="how-to-use-phrase-lists"></a>Comment utiliser une expression répertorie

Les exemples ci-dessous montrent comment créer une liste d’expressions à l’aide de la `PhraseListGrammar` objet.

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
> Le nombre maximal de listes de Phrase qui utilisera le Service de reconnaissance vocale pour correspondre à la reconnaissance vocale est 1024 phrases.

Vous pouvez également effacer les expressions associées le `PhraseListGrammar` par clear() appelant.

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
> Modifications apportées à un `PhraseListGrammar` sont pris en compte sur la reconnaissance suivante ou après une reconnexion aux Services de reconnaissance vocale de l’objet.

## <a name="next-steps"></a>Étapes suivantes

* [Documentation de référence de kit de développement logiciel de reconnaissance vocale](speech-sdk.md)
