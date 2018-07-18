---
title: Comptes de caractères de l’API de traduction de texte Microsoft Translator Text | Microsoft Docs
description: Comment l’API de traduction de texte Microsoft Translator Text compte les caractères.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 12/20/2017
ms.author: v-jansko
ms.openlocfilehash: ebe3e3606a0413730e1fbfd704a6403f77275f89
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35368644"
---
# <a name="how-the-microsoft-translator-text-api-counts-characters"></a>Comment l’API de traduction de texte Microsoft Translator Text compte les caractères

Microsoft Translator compte chaque caractère de l’entrée. Nous parlons de caractères dans le sens de caractères Unicode, pas dans le sens d’octets. Les substituts Unicode comptent comme deux caractères. Un espace blanc et une balise sont considérés comme des caractères. La longueur de la réponse n’a pas d’importance.

Les appels aux méthodes Detect et BreakSentence ne sont pas comptés dans l’utilisation de caractères. Toutefois, nous nous attendons à ce que la quantité d’appels aux méthodes Detect et BreakSentence soit raisonnable par rapport à l’utilisation des autres fonctions comptées. Microsoft se réserve le droit de commencer à compter les appels aux méthodes Detect et BreakSentence. 

Translate propose la détection automatique si vous omettez le paramètre de langue de départ. 

Vous trouverez plus d’informations sur les comptes de caractères dans la [FAQ de Translator](https://www.microsoft.com/en-us/translator/faq.aspx).
