---
title: Dictionnaire dynamique - API de traduction de texte Translator Text
titlesuffix: Azure Cognitive Services
description: Comment utiliser la fonctionnalité du dictionnaire dynamique de l’API de traduction de texte Translator Text.
services: cognitive-services
author: Jann-Skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: v-jansko
ms.openlocfilehash: 17ec3732a9d2ea59274989a5ebb052f1b73d701f
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58885456"
---
# <a name="how-to-use-the-dynamic-dictionary-feature-of-the-translator-text-api"></a>Comment utiliser la fonctionnalité du dictionnaire dynamique de l’API de traduction de texte Translator Text

Si vous connaissez déjà la traduction que vous souhaitez appliquer à un mot ou à une phrase, vous pouvez la fournir en tant que balisage dans la demande. Le dictionnaire dynamique n’est sûr que pour des noms composés, tels que des noms propres et des noms de produits.

**Syntaxe :**

<mstrans:dictionary translation=”traduction de phrase”>phrase</mstrans:dictionary>

**Exemple : fr-fr :**

Entrée source : le mot <mstrans:dictionary translation=\"wordomatic\">mot ou phrase</mstrans:dictionary> est une entrée de dictionnaire.

Sortie cible : Das Wort "wordomatic" ist ein Wörterbucheintrag.

Cette fonctionnalité opère de la même façon avec ou sans mode HTML.

Elle doit être utilisée avec parcimonie. La façon appropriée et de loin préférable de personnaliser une traduction consiste à utiliser Custom Translator. Custom Translator utilise totalement le contexte et les probabilités statistiques. Si vous avez ou pouvez créer des données d’apprentissage qui montrent votre mot ou phrase en contexte, vous obtenez de bien meilleurs résultats. Vous trouverez plus d’informations sur Custom Translator à l’adresse [https://aka.ms/CustomTranslator](https://aka.ms/CustomTranslator).
