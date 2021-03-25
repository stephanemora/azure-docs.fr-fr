---
title: Exemple de stratégie de Gestion des API - Autoriser une requête à l’aide d’un agent d’autorisation externe
titleSuffix: Azure API Management
description: Exemple de stratégie de Gestion des API Azure – Explique comment autoriser les demandes à l’aide d’un agent d’autorisation externe qui encapsule une logique d’authentification/autorisation personnalisée ou héritée.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/06/2018
ms.author: apimpm
ms.openlocfilehash: e38d92a13c9a66defc2d5090990b44a889cfd21c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92076230"
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