---
title: Exemple de stratégie de Gestion des API Azure – Autoriser la demande à l’aide d’un agent d’autorisation externe | Microsoft Docs
description: Exemple de stratégie de Gestion des API Azure – Explique comment autoriser les demandes à l’aide d’un agent d’autorisation externe qui encapsule une logique d’authentification/autorisation personnalisée ou héritée.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2018
ms.author: apimpm
ms.openlocfilehash: 70f43a058cfd3818dae1ccfa4b222a7d0d740aee
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46979401"
---
# <a name="authorize-requests-using-external-authorizer"></a>Autoriser les demandes à l’aide d’un agent d’autorisation externe

Cet article montre un exemple de stratégie de Gestion des API Azure qui explique comment sécuriser l’accès aux API à l’aide d’un agent d’autorisation externe qui encapsule une logique d’authentification/autorisation personnalisée. Pour définir ou modifier un code de stratégie, suivez les étapes décrites dans [Définir ou modifier une stratégie](../set-edit-policies.md). Pour voir d’autres exemples, consultez [Exemples de stratégie](../policy-samples.md).

## <a name="policy"></a>Stratégie

Collez le code dans le bloc **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Authorize requests using external authorizer.policy.xml)]

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les stratégies APIM :

+ [Politiques de restrictions d’accès](../api-management-access-restriction-policies.md)
+ [Exemples de stratégie](../policy-samples.md)
