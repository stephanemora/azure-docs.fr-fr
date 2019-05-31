---
title: Dictionnaire dynamique - API de traduction de texte Translator Text
titlesuffix: Azure Cognitive Services
description: Comment utiliser la fonctionnalité du dictionnaire dynamique de l’API de traduction de texte Translator Text.
services: cognitive-services
author: rajdeep-in
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: v-pawal
ms.openlocfilehash: 72c23270d6e8cdef55f07d0b702fd1858a7710f8
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389552"
---
# <a name="how-to-use-the-dynamic-dictionary-feature-of-the-translator-text-api"></a>Comment utiliser la fonctionnalité du dictionnaire dynamique de l’API de traduction de texte Translator Text

Si vous connaissez déjà la traduction que vous souhaitez appliquer à un mot ou à une phrase, vous pouvez la fournir en tant que balisage dans la demande. Le dictionnaire dynamique n’est sûr que pour des noms composés, tels que des noms propres et des noms de produits.

**Syntaxe :**

<mstrans:dictionary translation=”traduction de phrase”>phrase</mstrans:dictionary>

**Exemple : en-de :**

Entrée source : le mot <mstrans:dictionary translation=\"wordomatic\">mot ou phrase</mstrans:dictionary> est une entrée de dictionnaire.

Sortie cible : Das Wort "wordomatic" ist ein Wörterbucheintrag.

Cette fonctionnalité opère de la même façon avec ou sans mode HTML.

Elle doit être utilisée avec parcimonie. La façon appropriée et de loin préférable de personnaliser une traduction consiste à utiliser Custom Translator. Custom Translator utilise totalement le contexte et les probabilités statistiques. Si vous avez ou pouvez créer des données d’apprentissage qui montrent votre mot ou phrase en contexte, vous obtenez de bien meilleurs résultats. Vous trouverez plus d’informations sur Custom Translator à l’adresse [https://aka.ms/CustomTranslator](https://aka.ms/CustomTranslator).
