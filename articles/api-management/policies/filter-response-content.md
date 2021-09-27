---
title: Exemple de la stratégie de Gestion des API Azure - Filtrer le contenu de la réponse | Microsoft Docs
description: Exemple de la stratégie de Gestion des API Azure - Montre comment filtrer des éléments de données à partir de la charge utile de la réponse en fonction du produit associé à la requête.
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
ms.openlocfilehash: 4bcd9f9db9af45149028707ba2c055b29e2bb420
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128590617"
---
# <a name="filter-response-content"></a>Filtrer le contenu de la réponse

Cet article présente un exemple de la stratégie de Gestion des API Azure qui montre comment filtrer des éléments de données à partir de la charge utile de la réponse en fonction du produit associé à la requête. Pour définir ou modifier un code de stratégie, suivez les étapes décrites dans [Définir ou modifier une stratégie](../set-edit-policies.md). Pour voir d’autres exemples, consultez [Exemples de stratégie](../policy-reference.md).

## <a name="policy"></a>Policy

Collez le code dans le bloc **outbound**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Filter response content based on product name.policy.xml)]

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les stratégies APIM :

+ [Stratégies de transformation](../api-management-transformation-policies.md)
+ [Exemples de stratégie](../policy-reference.md)