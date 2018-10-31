---
title: 'Nombre de caractères : API de traduction de texte Translator Text'
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
ms.openlocfilehash: 8e04158d34765b8a077fc56f2108ea6d7b4b03a6
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49649192"
---
# <a name="how-the-translator-text-api-counts-characters"></a>Comment l’API de traduction de texte Translator Text compte les caractères

L’API de traduction de texte Translator Text compte chaque caractère de l’entrée. Nous parlons de caractères dans le sens de caractères Unicode, pas dans le sens d’octets. Les substituts Unicode comptent comme deux caractères. Un espace blanc et une balise sont considérés comme des caractères. La longueur de la réponse n’a pas d’importance.

Les appels aux méthodes Detect et BreakSentence ne sont pas comptés dans l’utilisation de caractères. Toutefois, nous nous attendons à ce que la quantité d’appels aux méthodes Detect et BreakSentence soit raisonnable par rapport à l’utilisation des autres fonctions comptées. Microsoft se réserve le droit de commencer à compter les appels aux méthodes Detect et BreakSentence.

Vous trouverez plus d’informations sur les comptes de caractères dans la [FAQ de Translator](https://www.microsoft.com/en-us/translator/faq.aspx).
