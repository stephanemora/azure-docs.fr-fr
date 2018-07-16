---
title: Comment choisir le mode de reconnaissance | Microsoft Docs
description: Comment choisir le meilleur mode de reconnaissance.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/15/2017
ms.author: zhouwang
ms.openlocfilehash: 4f02b683dde16b537ae5554e6435c068f0fcb808
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35368369"
---
# <a name="speech-recognition-modes"></a>Modes de reconnaissance vocale

Les API *Reconnaissance vocale* de Microsoft prennent en charge plusieurs modes de reconnaissance vocale. Choisissez le mode qui produit les meilleurs résultats de reconnaissance pour votre application.

| Mode | Description |
|---|---|
| *interactive* | Reconnaissance de type « commande et contrôle » pour les scénarios d’application utilisateur interactive. Les utilisateurs prononcent de courtes phrases pour commander une application. |
| *dictation* | Reconnaissance en continu pour les scénarios de dictée. Les utilisateurs prononcent des phrases plus longues qui sont affichées sous forme de texte. Les utilisateurs adoptent un ton plus formel. |
| *conversation* | Reconnaissance en continue pour retranscrire les conversations entre les personnes. Les utilisateurs adoptent un ton moins formel et peuvent alterner entre des phrases plus longues et d’autres plus courtes.

> [!NOTE]
> Ces modes sont applicables lorsque vous utilisez les [API REST](../GetStarted/GetStartedREST.md). Les [bibliothèques de client](../GetStarted/GetStartedClientLibraries.md) utilisent des paramètres différents pour spécifier le mode de reconnaissance. Pour plus d’informations, consultez la bibliothèque de client de votre choix.

Pour plus d’informations, consultez la section [Recognition Modes](../concepts.md#recognition-modes) (Modes de reconnaissance).
