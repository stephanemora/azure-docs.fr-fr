---
title: Exemple de stratégie de gestion des API Azure - Router la requête en fonction de la taille de son corps | Microsoft Docs
description: Exemple de stratégie de Gestion des API Azure - Montre comment router les requêtes en fonction de la taille de leurs corps.
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
ms.openlocfilehash: da21a7c83a61b618646c4c4d2f422756b04dc300
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2018
---
# <a name="route-the-request-based-on-the-size-of-its-body"></a>Router la requête en fonction de la taille de son corps

Cet article présente un exemple de stratégie de Gestion des API Azure qui montre comment router les requêtes en fonction de la taille de leurs corps. Pour définir ou modifier un code de stratégie, suivez les étapes décrites dans [Définir ou modifier une stratégie](../set-edit-policies.md). Pour voir d’autres exemples, consultez [Exemples de stratégie](../policy-samples.md).

## <a name="policy"></a>Stratégie

Collez le code dans le bloc **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Route requests based on size.policy.xml)]

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les stratégies APIM :

+ [Stratégies de transformation](../api-management-transformation-policies.md)
+ [Exemples de stratégie](../policy-samples.md)

