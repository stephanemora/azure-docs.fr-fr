---
title: Exemple de stratégie de Gestion des API Azure - Autoriser l’accès basé sur les revendications JWT
titleSuffix: Azure API Management
description: Exemple de la stratégie de Gestion des API Azure - Montre comment autoriser l’accès à des méthodes HTTP spécifiques sur une API basée sur les revendications JWT.
services: api-management
documentationcenter: ''
author: dlepow
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/13/2017
ms.author: danlep
ms.openlocfilehash: 72f9659bd93cea4cc0adcc638897ba2109fcc046
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131065643"
---
# <a name="authorize-access-based-on-jwt-claims"></a>Autoriser l’accès basé sur les revendications JWT

Cet article présente un exemple de la stratégie de Gestion des API Azure qui montre comment autoriser l’accès à des méthodes HTTP spécifiques sur une API basée sur les revendications JWT. Pour définir ou modifier un code de stratégie, suivez les étapes décrites dans [Définir ou modifier une stratégie](../set-edit-policies.md). Pour voir d’autres exemples, consultez [Exemples de stratégie](index.md).

## <a name="policy"></a>Policy

Collez le code dans le bloc **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Pre-authorize requests based on HTTP method with validate-jwt.policy.xml)]

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les stratégies APIM :

+ [Stratégies de transformation](../api-management-transformation-policies.md)
+ [Exemples de stratégie](../policy-reference.md)
