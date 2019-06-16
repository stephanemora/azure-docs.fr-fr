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
ms.openlocfilehash: 65ea8622187d0665e4680f4162ddff0bc01e6eb9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60306764"
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
