---
title: Empêcher la traduction de contenu – Translator
titleSuffix: Azure Cognitive Services
description: Empêchez la traduction de contenu avec Translator. Translator vous permet d’étiqueter du contenu pour qu’il ne soit pas traduit.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: 37d2e4c0131569ab50ebf49ff73b6adf7a420713
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2020
ms.locfileid: "83996174"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator"></a>Comment empêcher la traduction de contenu avec Translator

Translator vous permet d’étiqueter du contenu pour qu’il ne soit pas traduit. Par exemple, vous pouvez étiqueter du code, un nom de marque ou tout autre mot ou expression à ne pas traduire.

## <a name="methods-for-preventing-translation"></a>Méthodes visant à empêcher la traduction

1. Étiquetez votre contenu avec `notranslate`. Par défaut, cela fonctionne uniquement lorsque le textType d’entrée est défini en tant que langage HTML.

   Exemple :

   ```html
   <span class="notranslate">This will not be translated.</span>
   <span>This will be translated. </span>
   ```
   
   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

2. Utilisez le [dictionnaire dynamique](dynamic-dictionary.md) pour prescrire une traduction spécifique.

3. Ne passez pas la chaîne à Translator pour traduction.

4. Custom Translator : Utiliser un [dictionnaire dans Custom Translator](custom-translator/what-is-dictionary.md) pour prescrire la traduction d’une phrase avec une probabilité de 100 %.


## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Éviter la traduction dans votre appel de Translator](reference/v3-0-translate.md)
