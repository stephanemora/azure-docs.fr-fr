---
title: Exemple de stratégie de gestion des API Azure - Implémenter un modèle X-CSRF | Microsoft Docs
description: Exemple de stratégie de gestion des API Azure - Explique comment implémenter le modèle X-CSRF utilisé par de nombreuses API. Cet exemple est spécifique de la passerelle SAP.
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
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: 3a2067836a1488d117dced96f3935f2d1f8b1b48
ms.sourcegitcommit: e34afd967d66aea62e34d912a040c4622a737acb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/26/2018
ms.locfileid: "36946021"
---
# <a name="implement-x-csrf-pattern"></a>Implémenter le modèle X-CSRF

Cet article comporte un exemple de stratégie de gestion des API Azure qui vous explique la procédure d’implémentation du modèle X-CSRF utilisé par de nombreuses API. Cet exemple est spécifique de la passerelle SAP. Pour définir ou modifier un code de stratégie, suivez les étapes décrites dans [Définir ou modifier une stratégie](../set-edit-policies.md). Pour voir d’autres exemples, consultez [Exemples de stratégie](../policy-samples.md).

## <a name="policy"></a>Stratégie

Collez le code dans le bloc **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Get X-CSRF token from SAP gateway using send request.policy.xml)]

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les stratégies APIM :

+ [Stratégies de transformation](../api-management-transformation-policies.md)
+ [Exemples de stratégie](../policy-samples.md)

