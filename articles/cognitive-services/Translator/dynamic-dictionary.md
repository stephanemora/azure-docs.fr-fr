---
title: Dictionnaire dynamique - API de traduction de texte Translator Text
titleSuffix: Azure Cognitive Services
description: Cet article explique comment utiliser la fonctionnalité de dictionnaire dynamique de l’API Traduction de texte Translator Text Azure Cognitive Services.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 944bca8644da6127e73af04eb75d01697cd34399
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75446709"
---
# <a name="how-to-use-a-dynamic-dictionary"></a>Comment utiliser un dictionnaire dynamique

Si vous connaissez déjà la traduction que vous souhaitez appliquer à un mot ou à une phrase, vous pouvez la fournir en tant que balisage dans la demande. Le dictionnaire dynamique est sûr seulement pour des noms composés, comme des noms propres et des noms de produits.

**Syntaxe :**

<mstrans:dictionary translation=”traduction d’expression”>phrase</mstrans:dictionary>

**Configuration requise :**

* Les langues `From` et `To` doivent inclure l’anglais et une autre langue prise en charge. 
* Vous devez inclure le paramètre `From` dans votre demande de traduction d’API au lieu d’utiliser la fonctionnalité de détection automatique. 

**Exemple : en-de :**

Entrée source : `The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.`

Sortie cible : `Das Wort "wordomatic" ist ein Wörterbucheintrag.`

Cette fonctionnalité opère de la même façon avec ou sans mode HTML.

Utilisez la fonctionnalité avec modération. Une meilleure façon de personnaliser la traduction est d’utiliser Custom Translator. Custom Translator utilise totalement le contexte et les probabilités statistiques. Si vous avez ou pouvez créer des données d’apprentissage qui montrent votre mot ou phrase en contexte, vous obtenez de bien meilleurs résultats. Vous trouverez plus d’informations sur Custom Translator à l’adresse [https://aka.ms/CustomTranslator](https://aka.ms/CustomTranslator).
