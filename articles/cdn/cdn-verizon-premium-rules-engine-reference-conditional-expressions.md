---
title: Expressions conditionnelles du moteur de règles Azure CDN de Verizon Premium | Microsoft Docs
description: Documentation de référence pour Azure CDN de Verizon Premium fonctionnalités et conditions de correspondance du moteur de règles.
services: cdn
author: mdgattuso
ms.service: cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: c6e49e6fbc0c541ce9a8cd903eb313d61413257c
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66481533"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-conditional-expressions"></a>Azure CDN à partir d’expressions conditionnelles du moteur de règles Premium de Verizon

Cet article répertorie les descriptions détaillées des Expressions conditionnelles pour Azure Content Delivery Network (CDN) [moteur de règles](cdn-verizon-premium-rules-engine.md).

La première partie d’une règle est l’expression conditionnelle.

Expression conditionnelle | Description
-----------------------|-------------
IF | Une expression IF fait toujours partie de la première instruction dans une règle. Comme toutes les autres expressions conditionnelles, cette instruction IF doit être associée à une correspondance. Si aucune expression conditionnelle supplémentaire n’est définies, cette correspondance détermine le critère qui doit être satisfaite avant un ensemble de fonctionnalités peut être appliqué à une demande.
AND IF | Une expression AND IF ne peut être ajoutée qu’après les types d’expressions conditionnelles suivants : IF, AND IF. Elle indique qu’il existe une autre condition qui doit être remplie pour l’instruction IF initiale.
ELSE IF| Une expression ELSE IF spécifie une autre condition qui doit être remplie avant la mise en place d’un ensemble de fonctionnalités spécifiques à cette instruction ELSE IF. La présence d’une instruction ELSE IF indique la fin de l’instruction précédente. La seule expression conditionnelle pouvant être placée après une instruction ELSE IF est une autre instruction ELSE IF. Cela signifie qu’une instruction IF ELSE peut uniquement servir à spécifier une seule condition supplémentaire qui doit être respectée.

**Exemple**: ![Condition de correspondance CDN](./media/cdn-rules-engine-reference/cdn-rules-engine-conditional-expression.png)

 > [!TIP]
   > Une règle ultérieure peut remplacer les actions spécifiées par une règle antérieure.
   > Exemple : Une règle de fourre-tout sécurise toutes les demandes via l’authentification basée sur le jeton. Une autre règle peut être créée directement en dessous pour générer une exception pour certains types de requêtes.

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble d’Azure CDN](cdn-overview.md)
- [Informations de référence du moteur de règles](cdn-verizon-premium-rules-engine-reference.md)
- [Conditions de correspondance du moteur de règles](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Fonctionnalités du moteur de règles](cdn-verizon-premium-rules-engine-reference-features.md)
- [Remplacement du comportement HTTP par défaut à l’aide du moteur de règles](cdn-verizon-premium-rules-engine.md)