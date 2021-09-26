---
title: Exemple de stratégie de gestion des API Azure - Implémenter un modèle X-CSRF | Microsoft Docs
description: Exemple de stratégie de gestion des API Azure - Explique comment implémenter le modèle X-CSRF utilisé par de nombreuses API. Cet exemple est spécifique de la passerelle SAP.
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
ms.openlocfilehash: 0e6bcd315600739df6e9b5ec6df0d5297772fb83
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128606605"
---
# <a name="implement-x-csrf-pattern"></a>Implémenter le modèle X-CSRF

Cet article comporte un exemple de stratégie de gestion des API Azure qui vous explique la procédure d’implémentation du modèle X-CSRF utilisé par de nombreuses API. Cet exemple est spécifique de la passerelle SAP. Pour définir ou modifier un code de stratégie, suivez les étapes décrites dans [Définir ou modifier une stratégie](../set-edit-policies.md). Pour voir d’autres exemples, consultez [Exemples de stratégie](../policy-reference.md).

## <a name="policy"></a>Policy

Collez le code dans le bloc **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Get X-CSRF token from SAP gateway using send request.policy.xml)]

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les stratégies APIM :

+ [Stratégies de transformation](../api-management-transformation-policies.md)
+ [Exemples de stratégie](../policy-reference.md)