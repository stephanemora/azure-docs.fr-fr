---
title: Guide pratique pour choisir le mode de reconnaissance vocale Bing | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Comment choisir le meilleur mode de reconnaissance dans l’API Reconnaissance vocale Bing
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX
ms.openlocfilehash: a39b357a26823e322d4e902f2d99b67488bbf2df
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46950877"
---
# <a name="bing-speech-recognition-modes"></a>Modes de reconnaissance vocale dans l’API Reconnaissance vocale Bing

Les API Reconnaissance de la parole-texte Bing prennent en charge plusieurs modes de reconnaissance vocale. Choisissez le mode qui produit les meilleurs résultats de reconnaissance pour votre application.

| Mode | Description |
|---|---|
| *interactive* | Reconnaissance de type « commande et contrôle » pour les scénarios d’application utilisateur interactive. Les utilisateurs prononcent de courtes phrases pour commander une application. |
| *dictation* | Reconnaissance en continu pour les scénarios de dictée. Les utilisateurs prononcent des phrases plus longues qui sont affichées sous forme de texte. Les utilisateurs adoptent un ton plus formel. |
| *conversation* | Reconnaissance en continue pour retranscrire les conversations entre les personnes. Les utilisateurs adoptent un ton moins formel et peuvent alterner entre des phrases plus longues et d’autres plus courtes.

> [!NOTE]
> Ces modes sont applicables lorsque vous utilisez les [API REST](../GetStarted/GetStartedREST.md). Les [bibliothèques de client](../GetStarted/GetStartedClientLibraries.md) utilisent des paramètres différents pour spécifier le mode de reconnaissance. Pour plus d’informations, consultez la bibliothèque de client de votre choix.

Pour plus d’informations, consultez la section [Recognition Modes](../concepts.md#recognition-modes) (Modes de reconnaissance).
