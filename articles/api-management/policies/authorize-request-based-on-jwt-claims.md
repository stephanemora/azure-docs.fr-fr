---
title: Exemple de la stratégie de Gestion des API Azure - Autoriser l’accès basé sur les revendications JWT | Microsoft Docs
description: Exemple de la stratégie de Gestion des API Azure - Montre comment autoriser l’accès à des méthodes HTTP spécifiques sur une API basée sur les revendications JWT.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: dd99d9ed3eebe6ada60511b3f16c53b0d57a65d6
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70067806"
---
# <a name="authorize-access-based-on-jwt-claims"></a>Autoriser l’accès basé sur les revendications JWT

Cet article présente un exemple de la stratégie de Gestion des API Azure qui montre comment autoriser l’accès à des méthodes HTTP spécifiques sur une API basée sur les revendications JWT. Pour définir ou modifier un code de stratégie, suivez les étapes décrites dans [Définir ou modifier une stratégie](../set-edit-policies.md). Pour voir d’autres exemples, consultez [Exemples de stratégie](../policy-samples.md).

## <a name="policy"></a>Stratégie

Collez le code dans le bloc **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Pre-authorize requests based on HTTP method with validate-jwt.policy.xml)]

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les stratégies APIM :

+ [Stratégies de transformation](../api-management-transformation-policies.md)
+ [Exemples de stratégie](../policy-samples.md)

