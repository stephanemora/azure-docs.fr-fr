---
title: Qu’est-ce que l’API de traduction de texte Translator Text ? - API de traduction de texte Translator Text
titlesuffix: Azure Cognitive Services
description: Intégrez l’API de traduction de texte Translator Text à vos applications, sites web, outils et autres solutions, pour offrir une expérience utilisateur multilingue.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: overview
ms.date: 12/06/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 1fb608f6715d3f48446451d437535b3ddfb0677a
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55455250"
---
# <a name="what-is-translator-text-api"></a>Qu’est-ce que l’API de traduction de texte Translator Text ?

Il est facile d’intégrer l’API de traduction de texte Translator Text dans vos applications, sites web, outils et solutions. Cette interface vous permet d’ajouter des expériences utilisateur multilingues dans [plus de 60 langues](languages.md). Elle peut être utilisée sur tout type de plateforme matérielle possédant un système d’exploitation adapté à la traduction de texte en texte.

L’API de traduction de texte Translator Text fait partie de la collection d’[API Cognitive Services](https://docs.microsoft.com/azure/#pivot=products&panel=ai) Azure d’algorithmes de machine learning et d’intelligence artificielle dans le cloud, que vous pouvez utiliser directement dans vos projets de développement.

## <a name="about-microsoft-translator"></a>À propos de Microsoft Translator

Microsoft Translator est un service de traduction informatique. Son service principal est l’API de traduction de texte Translator Text, qui alimente un certain nombre de produits et services Microsoft. Il est utilisé par des milliers d’entreprises à travers le monde, dans leurs applications et flux de travail qui proposent leur contenu à un public international.

La traduction vocale, alimentée par l’API de traduction de texte Translator Text, est également disponible via le [Service Microsoft Speech](https://docs.microsoft.com/azure/cognitive-services/speech-service/). Il combine les fonctionnalités de l’API de traduction de conversation Translator Speech,de l’API Reconnaissance vocale Bing et du Custom Speech Service (préversion) en un service unifié et entièrement personnalisable. Le Speech Service remplace l’API de traduction de conversation Translator Speech, qui sera désactivé le 15 octobre 2019.

## <a name="language-support"></a>Support multilingue

Microsoft Translator fournit une prise en charge multilingue pour la traduction, translittération, détection de la langue et les dictionnaires. Consultez [Prise en charge de la langue](language-support.md) pour obtenir une liste complète, ou accéder à la liste par programmation avec l’[API REST](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages).  

## <a name="language-customization"></a>Personnalisation de la langue

En tant qu’extension du service Microsoft Translator central, Custom Translator est utilisable conjointement avec l’API de traduction de texte Translator Text pour vous aider à personnaliser le système de traduction neuronal et à améliorer la traduction de la terminologie et du style qui vous sont propres.

Avec Custom Translator, vous pouvez créer des systèmes de traduction qui gèrent la terminologie utilisée dans votre entreprise ou secteur d’activité. Votre système de traduction personnalisé s’intégrera alors facilement à vos applications, flux de travail et sites web existants, sur plusieurs types d’appareils, par le biais de l’API de traduction de texte Microsoft Translator Text standard, à l’aide du paramètre de catégorie.

Apprenez-en davantage sur la [personnalisation de la langue](customization.md).

## <a name="microsoft-translator-neural-machine-translation"></a>Traduction automatique neuronale Microsoft Translator

La traduction automatique neuronale (NMT) constitue le nouveau standard en matière de traductions automatiques de grande qualité reposant sur l’intelligence artificielle. Elle remplace la technologie de traduction automatique statistique (SMT) héritée dont la qualité a plafonné à la moitié des années 2010.

La NMT fournit de meilleures traductions que la SMT, non seulement en termes de qualité par rapport à une traduction brute, mais également parce qu’elle donne des résultats plus fluides et plus naturels. La cause principale de cette fluidité est que la NMT utilise le contexte entier d’une phrase pour traduire des mots. La SMT ne prend en compte que le contexte immédiat de quelques mots avant et après chaque mot.

Les modèles NMT sont au cœur de l’API et ne sont pas visibles aux utilisateurs finaux. La seule différence notable réside dans l’amélioration de la qualité des traductions, en particulier pour les langues telles que le chinois, le japonais et l’arabe.

Découvrez-en plus sur le [fonctionnement de la traduction automatique neuronale](https://www.microsoft.com/en-us/translator/mt.aspx#nnt).

## <a name="next-steps"></a>Étapes suivantes

- Consultez les [détails de tarification](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/).

- [Inscrivez-vous](translator-text-how-to-signup.md) pour obtenir une clé d’accès.

- Le [démarrage rapide](quickstarts/csharp.md) est une procédure pas à pas relative aux appels d’API REST, écrite en C#. Découvrez comment traduire du texte dans une autre langue avec un minimum de code.

- La [documentation de référence de l’API](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference) fournit des informations techniques sur l’API.

## <a name="see-also"></a>Voir aussi

- [Page Documentation Cognitive Services](https://docs.microsoft.com/azure/#pivot=products&panel=ai)
- [Page de produits Cognitive Services](https://azure.microsoft.com/services/cognitive-services/)
- [Informations sur les solutions et la facturation](https://www.microsoft.com/en-us/translator/default.aspx)
