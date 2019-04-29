---
title: Exemple de la stratégie de Gestion des API Azure - Filtrer le contenu de la réponse | Microsoft Docs
description: Exemple de la stratégie de Gestion des API Azure - Montre comment filtrer des éléments de données à partir de la charge utile de la réponse en fonction du produit associé à la requête.
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
ms.openlocfilehash: 26829dfc04acdac2a25fe0d4fdc3e95e4d219057
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60637061"
---
# <a name="filter-response-content"></a>Filtrer le contenu de la réponse

Cet article présente un exemple de la stratégie de Gestion des API Azure qui montre comment filtrer des éléments de données à partir de la charge utile de la réponse en fonction du produit associé à la requête. Pour définir ou modifier un code de stratégie, suivez les étapes décrites dans [Définir ou modifier une stratégie](../set-edit-policies.md). Pour voir d’autres exemples, consultez [Exemples de stratégie](../policy-samples.md).

## <a name="policy"></a>Stratégie

Collez le code dans le bloc **outbound**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Filter response content based on product name.policy.xml)]

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les stratégies APIM :

+ [Stratégies de transformation](../api-management-transformation-policies.md)
+ [Exemples de stratégie](../policy-samples.md)

