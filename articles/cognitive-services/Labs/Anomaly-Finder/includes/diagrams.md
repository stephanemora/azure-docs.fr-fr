---
title: Fichier Include
description: Fichier Include
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: 79ae38db73d55021572d04f693e5cb809e9bd056
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55228965"
---
Les données sont retournées avec la valeur attendue et les marges supérieure et inférieure par défaut. Dans la pratique, vous pouvez définir un paramètre [sensitivity], puis utiliser (ExpectedValue + sensitivity * UpperMargin) comme limite supérieure et (ExpectedValue - sensitivity * LowerMargin) comme limite inférieure pour régler le point d’anomalie par vous-même. La valeur de [sensitivity] doit être supérieure à 1. Vous trouverez ci-dessous des diagrammes pour le réglage.

> [!NOTE]
> Les diagrammes ne sont pas générés par l’exemple d’application. Ils sont créés par un outil distinct avec l’exemple d’application.

![Réglage : sensitivity = 1.0](../media/sensitivity_1.png)
![Réglage : sensitivity = 1.5](../media/sensitivity_1.5.png)
![Réglage : sensitivity = 2](../media/sensitivity_2.png)
![Réglage : sensitivity = 3.5](../media/sensitivity_3.5.png)
