---
title: Traduire derrière des pare-feu - API de traduction de texte Translator Text
titlesuffix: Azure Cognitive Services
description: Traduisez derrière des pare-feu IP avec l’API de traduction de texte Translator Text.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: conceptual
ms.date: 11/20/2018
ms.author: v-jansko
ms.openlocfilehash: bf1a63d996a1ed2021654c96576eb9dac2cff8e6
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2018
ms.locfileid: "52683331"
---
# <a name="how-to-translate-behind-ip-firewalls-with-the-translator-text-api"></a>Comment traduire derrière des pare-feu IP avec l’API de traduction de texte Translator Text

L’API de traduction de texte Translator Text peut traduire derrière des pare-feu utilisant le filtrage de noms de domaine ou d’adresses IP. Le filtrage de noms de domaine est la méthode préférée. Nous vous **déconseillons** d’exécuter Microsoft Translator derrière un pare-feu avec des adresses IP filtrées. Cette configuration est susceptible de planter à tout moment. 

## <a name="translator-ip-addresses"></a>Adresses IP de Translator
En date du 20 novembre 2018, les adresses IP pour l’API de traduction de texte Translator Text (api.cognitive.microsofttranslator.com) sont les suivantes :

* **Asie-Pacifique :** 40.90.139.163, 104.44.89.44
* **Europe :** 40.90.138.4, 40.90.141.99
* **Amérique du Nord :** 40.90.139.36, 40.90.139.2


## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Traduire derrière des pare-feu IP dans votre appel d’API Translator](reference/v3-0-translate.md)