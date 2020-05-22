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
ms.date: 03/20/2020
ms.author: swmachan
ms.openlocfilehash: 33939976a0824ce8afeb2e6f6fb19e7033098683
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592693"
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
