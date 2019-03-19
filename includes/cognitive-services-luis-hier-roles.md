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
ms.openlocfilehash: 3803bc7f1e0da01fbaa8aa11bc6e8fc5c508b5ae
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/23/2019
ms.locfileid: "56741167"
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
