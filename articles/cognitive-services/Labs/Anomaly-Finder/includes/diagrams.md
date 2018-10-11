---
title: Fichier Include
description: Fichier Include
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.component: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: a1cda1cea2089363331ae437cb7ad802429779f4
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2018
ms.locfileid: "48888694"
---
Les données sont retournées avec la valeur attendue et les marges supérieure et inférieure par défaut. Dans la pratique, vous pouvez définir un paramètre [sensitivity], puis utiliser (ExpectedValue + sensitivity * UpperMargin) comme limite supérieure et (ExpectedValue - sensitivity * LowerMargin) comme limite inférieure pour régler le point d’anomalie par vous-même. La valeur de [sensitivity] doit être supérieure à 1. Vous trouverez ci-dessous des diagrammes pour le réglage.

> [!NOTE]
> Les diagrammes ne sont pas générés par l’exemple d’application. Ils sont créés par un outil distinct avec l’exemple d’application.

![Réglage : sensitivity = 1.0](../media/sensitivity_1.png)
![Réglage : sensitivity = 1.5](../media/sensitivity_1.5.png)
![Réglage : sensitivity = 2](../media/sensitivity_2.png)
![Réglage : sensitivity = 3.5](../media/sensitivity_3.5.png)