---
title: Traduire derrière des pare-feu - Translator
titleSuffix: Azure Cognitive Services
description: Le service Translator d’Azure Cognitive Services peut traduire derrière des pare-feu utilisant le filtrage de noms de domaine ou d’adresses IP.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: dce41879f77d0bed44daa89c1dabbcc3f92e145e
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592523"
---
# <a name="how-to-translate-behind-ip-firewalls-with-translator"></a>Comment traduire derrière des pare-feu IP avec le service Translator

Le service Translator peut traduire derrière des pare-feu utilisant le filtrage de noms de domaine ou d’adresses IP. Le filtrage de noms de domaine est la méthode préférée. Nous vous **déconseillons** d’exécuter Microsoft Translator derrière un pare-feu avec des adresses IP filtrées. Cette configuration est susceptible de planter à tout moment.

## <a name="translator-ip-addresses"></a>Adresses IP de Translator
Au 21 août 2019, les adresses IP pour api.cognitive.microsofttranslator.com - Translator sont les suivantes :

* **Asie-Pacifique :** 20.40.125.208, 20.43.88.240, 20.184.58.62, 40.90.139.163, 104.44.89.44
* **Europe :** 40.90.138.4, 40.90.141.99, 51.105.170.64, 52.155.218.251
* **Amérique du Nord :** 40.90.139.36, 40.90.139.2, 40.119.2.134, 52.224.200.129, 52.249.207.163

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Traduire derrière des pare-feu IP dans Translator](reference/v3-0-translate.md)
