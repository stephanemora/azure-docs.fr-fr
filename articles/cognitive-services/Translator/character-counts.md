---
title: 'Nombre de caractères : API de traduction de texte Translator Text'
titlesuffix: Azure Cognitive Services
description: Comment l’API de traduction de texte Translator Text compte les caractères.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 12/20/2017
ms.author: v-jansko
ms.openlocfilehash: c56622ee5e25fc422d02cec6ecfaa1f847e9e769
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55226432"
---
# <a name="how-the-translator-text-api-counts-characters"></a>Comment l’API de traduction de texte Translator Text compte les caractères

L’API de traduction de texte Translator Text compte chaque caractère de l’entrée. Nous parlons de caractères dans le sens de caractères Unicode, pas dans le sens d’octets. Les substituts Unicode comptent comme deux caractères. Un espace blanc et une balise sont considérés comme des caractères. La longueur de la réponse n’a pas d’importance.

Les appels aux méthodes Detect et BreakSentence ne sont pas comptés dans l’utilisation de caractères. Toutefois, nous nous attendons à ce que la quantité d’appels aux méthodes Detect et BreakSentence soit raisonnable par rapport à l’utilisation des autres fonctions comptées. Microsoft se réserve le droit de commencer à compter les appels aux méthodes Detect et BreakSentence.

Vous trouverez plus d’informations sur les comptes de caractères dans la [FAQ de Translator](https://www.microsoft.com/en-us/translator/faq.aspx).
