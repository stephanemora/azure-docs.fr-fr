---
title: Empêcher la traduction de contenu - API de traduction de texte Translator Text
titlesuffix: Azure Cognitive Services
description: Empêchez la traduction de contenu avec l’API de traduction de texte Translator Text.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: conceptual
ms.date: 11/20/2018
ms.author: v-jansko
ms.openlocfilehash: b012201bcb9e0aece3d7e1ddd4f771643088af76
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2018
ms.locfileid: "52683347"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator-text-api"></a>Comment empêcher la traduction de contenu avec l’API de traduction de texte Translator Text

L’API de traduction de texte Translator Text vous permet d’étiqueter du contenu pour qu’il ne soit pas traduit. Par exemple, vous pouvez étiqueter du code, un nom de marque ou tout autre mot ou expression à ne pas traduire. 

## <a name="methods-for-preventing-translation"></a>Méthodes visant à empêcher la traduction
1. Créez une séquence d’échappement à l’aide d’un hashtag Twitter, par exemple @somethingtopassthrough ou #somethingtopassthrough. Annulez la séquence d’échappement après la traduction.

2. Étiquetez votre contenu avec `notranslate`.

   Exemple :

   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

3. Utilisez le [dictionnaire dynamique](dynamic-dictionary.md) pour prescrire une traduction spécifique.

4. Ne passez pas la chaîne à l’API de traduction de texte Translator Text pour traduction.

5. Custom Translator : Utiliser un [dictionnaire dans Custom Translator](custom-translator/what-is-dictionary.md) pour prescrire la traduction d’une phrase avec une probabilité de 100 %.


## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Éviter la traduction dans un appel d’API Translator](reference/v3-0-translate.md)
