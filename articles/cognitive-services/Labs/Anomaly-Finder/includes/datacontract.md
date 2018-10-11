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
ms.openlocfilehash: 9280790f6692096a0b3909c9d1dfab2e94a8c0d7
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2018
ms.locfileid: "48904494"
---
Avec l’[API Recherche d’anomalies](https://labs.cognitive.microsoft.com/en-us/project-anomaly-finder), vous pouvez télécharger des données de série chronologique au format JSON sur le point de terminaison de l’API, puis consulter le résultat de la réponse de l’API. Vous pouvez télécharger les données de série chronologique, chaque point de données inclut :  
* Horodatage - Horodatage du point de données. Assurez-vous d’utiliser une chaîne de date et d’heure UTC, comme « 2017-08-01T00:00:00Z ».
* Valeur - Mesure de ce point de données.

Voici les éléments qui composent les résultats :
* Period : périodicité utilisée par l’API pour détecter les anomalies
* WarningText : message d’avertissement possible
* ExpectedValue : valeur prédite par le modèle basé sur l’apprentissage
* IsAnomaly : résultat indiquant si les points de données sont des anomalies ou non dans les deux directions (pics ou creux).
* IsAnomaly_Neg : résultat indiquant si les points de données sont des anomalies dans une direction négative (chutes)
* IsAnomaly_Pos : résultat indiquant si les points de données sont des anomalies dans une direction positive (pics)
* UpperMargin : la somme d’ExpectedValue et d’UpperMargin détermine la limite supérieure où le point de données est encore considéré comme normal
* LowerMargin : (ExpectedValue - LowerMargin) détermine la limite inférieure où le point de données est encore considéré comme normal.

Les détails du contrat de données se trouvent [ici](../apiref.md).

