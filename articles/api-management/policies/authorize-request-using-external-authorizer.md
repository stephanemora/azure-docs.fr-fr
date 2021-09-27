---
title: Exemple de stratégie de Gestion des API - Autoriser une requête à l’aide d’un agent d’autorisation externe
titleSuffix: Azure API Management
description: Exemple de stratégie de Gestion des API Azure – Explique comment autoriser les demandes à l’aide d’un agent d’autorisation externe qui encapsule une logique d’authentification/autorisation personnalisée ou héritée.
services: api-management
documentationcenter: ''
author: dlepow
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/06/2018
ms.author: danlep
ms.openlocfilehash: 4f91eb6da5cea64eac4b324391a09e879e7ebb27
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128670231"
---
# <a name="authorize-requests-using-external-authorizer"></a>Autoriser les demandes à l’aide d’un agent d’autorisation externe

Cet article montre un exemple de stratégie de Gestion des API Azure qui explique comment sécuriser l’accès aux API à l’aide d’un agent d’autorisation externe qui encapsule une logique d’authentification/autorisation personnalisée. Pour définir ou modifier un code de stratégie, suivez les étapes décrites dans [Définir ou modifier une stratégie](../set-edit-policies.md). Pour voir d’autres exemples, consultez [Exemples de stratégie](../policy-reference.md).

## <a name="policy"></a>Policy

Collez le code dans le bloc **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Authorize requests using external authorizer.policy.xml)]

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les stratégies APIM :

+ [Politiques de restrictions d’accès](../api-management-access-restriction-policies.md)
+ [Exemples de stratégie](../policy-reference.md)