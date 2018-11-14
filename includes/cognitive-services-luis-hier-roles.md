---
title: Fichier Include
description: Fichier Include
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: include
ms.custom: include file
ms.date: 10/23/2018
ms.author: diberry
ms.openlocfilehash: 0ad3b360611a12b95568e9a20f13a57c93b2e603
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2018
ms.locfileid: "51208302"
---
## <a name="roles-versus-hierarchical-entities"></a>Rôles et entités hiérarchiques

Faut-il utiliser une entité ou un modèle hiérarchique avec une entité simple avec des rôles ? 

Cela dépend. Les modèles et les exemples d’énoncés sont similaires car ils représentent l’énoncé d’un utilisateur et sont spécifiques à une intention.  

Si les énoncés ne disposent pas d’un modèle clair, utilisez des entités hiérarchiques. 

|Entités hiérarchiques|Une entité simple avec des rôles|
|--|--|
|Disponible dans les exemples d’énoncés et les modèles d’intention|Disponible uniquement dans les modèles|
|Doit disposer d’exemples d’énoncés avec des entités enfant marquées dans les intentions|Les rôles ne peuvent pas être marqués dans les exemples d’énoncés dans l’intention|
|Peut avoir besoin de **plus** d’exemples d’énoncés dans l’intention pour extraire l’entité|Doit avoir besoin de **moins** d’exemples d’énoncés dans l’intention|
