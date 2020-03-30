---
title: Exemple de stratégie de Gestion des API Azure - Autoriser l’accès basé sur les revendications JWT
titleSuffix: Azure API Management
description: Exemple de la stratégie de Gestion des API Azure - Montre comment autoriser l’accès à des méthodes HTTP spécifiques sur une API basée sur les revendications JWT.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: 1b84854688fbdcc017b16698dfebfe54a7210110
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422181"
---
# <a name="authorize-access-based-on-jwt-claims"></a>Autoriser l’accès basé sur les revendications JWT

Cet article présente un exemple de la stratégie de Gestion des API Azure qui montre comment autoriser l’accès à des méthodes HTTP spécifiques sur une API basée sur les revendications JWT. Pour définir ou modifier un code de stratégie, suivez les étapes décrites dans [Définir ou modifier une stratégie](../set-edit-policies.md). Pour voir d’autres exemples, consultez [Exemples de stratégie](../policy-samples.md).

## <a name="policy"></a>Stratégie

Collez le code dans le bloc **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Pre-authorize requests based on HTTP method with validate-jwt.policy.xml)]

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les stratégies APIM :

+ [Stratégies de transformation](../api-management-transformation-policies.md)
+ [Exemples de stratégie](../policy-samples.md)

