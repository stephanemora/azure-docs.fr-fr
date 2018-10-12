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
ms.date: 08/02/2018
ms.author: diberry
ms.openlocfilehash: dd99218765c873120c4117a3ec1712fe0a605e66
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44164585"
---
**Question** : faut-il utiliser une entité ou un modèle hiérarchique avec une entité simple avec des rôles ? 

**Réponse** : cela dépend. Les modèles et les exemples d’énoncés sont similaires car ils représentent l’énoncé d’un utilisateur et sont spécifiques à une intention.  

Si les énoncés ne disposent pas d’un modèle clair, utilisez des entités hiérarchiques. 

|Entités hiérarchiques|Une entité simple avec des rôles|
|--|--|
|doit disposer d’exemples d’énoncés avec des entités enfant marquées dans les intentions|doit disposer d’exemples d’énoncés, **les rôles ne peuvent pas être marqués dans les intentions**|
|peut être utilisée dans des modèles|**doit** être utilisée dans des modèles|
|peut avoir besoin de **plus** d’exemples d’énoncés dans l’intention|peut avoir besoin de **moins d’exemples d’énoncés dans l’intention|