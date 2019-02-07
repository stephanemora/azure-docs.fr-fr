---
title: Fichier Include
description: Fichier Include
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 10/23/2018
ms.author: diberry
ms.openlocfilehash: dee31fca841ea309128681637cc41fa0fb1e7aea
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55480474"
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
