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
ms.openlocfilehash: f6475b272239e9352211a80985e1b46da9c6f8e0
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2018
---
# <a name="filter-response-content"></a>Filtrer le contenu de la réponse

Cet article présente un exemple de la stratégie de Gestion des API Azure qui montre comment filtrer des éléments de données à partir de la charge utile de la réponse en fonction du produit associé à la requête. Pour définir ou modifier un code de stratégie, suivez les étapes décrites dans [Définir ou modifier une stratégie](../set-edit-policies.md). Pour voir d’autres exemples, consultez [Exemples de stratégie](../policy-samples.md).

## <a name="policy"></a>Stratégie

Collez le code dans le bloc **outbound**.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Filter response content based on product name.policy.xml)]

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les stratégies APIM :

+ [Stratégies de transformation](../api-management-transformation-policies.md)
+ [Exemples de stratégie](../policy-samples.md)

