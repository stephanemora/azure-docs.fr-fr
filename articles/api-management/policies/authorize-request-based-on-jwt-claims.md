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
ms.openlocfilehash: 1df5f4b874ff3e5e05ab6b72a786364916ae0f53
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128618815"
---
# <a name="authorize-access-based-on-jwt-claims"></a>Autoriser l’accès basé sur les revendications JWT

Cet article présente un exemple de la stratégie de Gestion des API Azure qui montre comment autoriser l’accès à des méthodes HTTP spécifiques sur une API basée sur les revendications JWT. Pour définir ou modifier un code de stratégie, suivez les étapes décrites dans [Définir ou modifier une stratégie](../set-edit-policies.md). Pour voir d’autres exemples, consultez [Exemples de stratégie](../policy-reference.md).

## <a name="policy"></a>Policy

Collez le code dans le bloc **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Pre-authorize requests based on HTTP method with validate-jwt.policy.xml)]

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les stratégies APIM :

+ [Stratégies de transformation](../api-management-transformation-policies.md)
+ [Exemples de stratégie](../policy-reference.md)