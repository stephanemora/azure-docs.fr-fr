---
title: Dictionnaire dynamique de l’API de traduction de texte Translator Text Microsoft | Microsoft Docs
description: Comment utiliser la fonctionnalité de dictionnaire dynamique de l’API de traduction de texte Translator Text Microsoft.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: a18348c9786669ac41c4e149577d97cd631d5531
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35370688"
---
# <a name="how-to-use-the-dynamic-dictionary-feature-of-the-microsoft-translator-text-api"></a>Comment utiliser la fonctionnalité de dictionnaire dynamique de l’API de traduction de texte Translator Text Microsoft

Si vous connaissez déjà la traduction que vous souhaitez appliquer à un mot ou à une phrase, vous pouvez la fournir en tant que balisage dans la demande. Le dictionnaire dynamique n’est sûr que pour des noms composés, tels que des noms propres et des noms de produits. 

**Syntaxe :** 

<mstrans:dictionary translation=”traduction de phrase”>phrase</mstrans:dictionary>

**Exemple : en-de :**

Entrée source : le mot <mstrans:dictionary translation=\"wordomatic\">mot ou phrase</mstrans:dictionary> est une entrée de dictionnaire.

Sortie cible : Das Wort "wordomatic" ist ein Wörterbucheintrag.

Cette fonctionnalité opère de la même façon avec ou sans mode HTML. 

Elle doit être utilisée avec parcimonie. La façon appropriée et de loin préférable de personnaliser une traduction consiste à utiliser le Microsoft Translator Hub. Celui-ci utilise totalement le contexte et les probabilités statistiques. Si vous avez ou pouvez vous permettre de créer des données d’apprentissage qui montrent votre mot ou phrase en contexte, vous obtenez de bien meilleurs résultats. Pour plus d’informations sur le Hub, voir [http://hub.microsofttranslator.com](http://hub.microsofttranslator.com).

