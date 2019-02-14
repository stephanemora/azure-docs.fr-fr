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
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: 6140361d3cffaf7938aa8f24b6f0c3d27784af88
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55876009"
---
# <a name="how-to-use-the-dynamic-dictionary-feature-of-the-translator-text-api"></a>Comment utiliser la fonctionnalité du dictionnaire dynamique de l’API de traduction de texte Translator Text

Si vous connaissez déjà la traduction que vous souhaitez appliquer à un mot ou à une phrase, vous pouvez la fournir en tant que balisage dans la demande. Le dictionnaire dynamique n’est sûr que pour des noms composés, tels que des noms propres et des noms de produits.

**Syntaxe :**

<mstrans:dictionary translation=”traduction de phrase”>phrase</mstrans:dictionary>

**Exemple : en-de :**

Entrée source : le mot <mstrans:dictionary translation=\"wordomatic\">mot ou phrase</mstrans:dictionary> est une entrée de dictionnaire.

Sortie cible : Das Wort "wordomatic" ist ein Wörterbucheintrag.

Cette fonctionnalité opère de la même façon avec ou sans mode HTML.

Elle doit être utilisée avec parcimonie. La façon appropriée et de loin préférable de personnaliser une traduction consiste à utiliser Custom Translator. Custom Translator utilise totalement le contexte et les probabilités statistiques. Si vous avez ou pouvez créer des données d’apprentissage qui montrent votre mot ou phrase en contexte, vous obtenez de bien meilleurs résultats. Vous trouverez plus d’informations sur Custom Translator à l’adresse [http://aka.ms/CustomTranslator](https://aka.ms/CustomTranslator).
