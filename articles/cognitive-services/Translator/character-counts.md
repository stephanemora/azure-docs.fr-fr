---
title: Nombres de caractère de l’API de traduction de texte Translator Text
titlesuffix: Azure Cognitive Services
description: Comment l’API de traduction de texte Translator Text compte les caractères.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: conceptual
ms.date: 12/20/2017
ms.author: v-jansko
ms.openlocfilehash: c6234a46ae55d73739dcc23110c5e0f6375c3f96
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2018
ms.locfileid: "46128738"
---
# <a name="how-the-translator-text-api-counts-characters"></a>Comment l’API de traduction de texte Translator Text compte les caractères

L’API de traduction de texte Translator Text compte chaque caractère de l’entrée. Nous parlons de caractères dans le sens de caractères Unicode, pas dans le sens d’octets. Les substituts Unicode comptent comme deux caractères. Un espace blanc et une balise sont considérés comme des caractères. La longueur de la réponse n’a pas d’importance.

Les appels aux méthodes Detect et BreakSentence ne sont pas comptés dans l’utilisation de caractères. Toutefois, nous nous attendons à ce que la quantité d’appels aux méthodes Detect et BreakSentence soit raisonnable par rapport à l’utilisation des autres fonctions comptées. Microsoft se réserve le droit de commencer à compter les appels aux méthodes Detect et BreakSentence. 

Vous trouverez plus d’informations sur les comptes de caractères dans la [FAQ de Translator](https://www.microsoft.com/en-us/translator/faq.aspx).
