---
title: Exemple de stratégie de gestion des API Azure – Autoriser l’accès à l’aide du jeton Google OAuth | Microsoft Docs
description: Exemple de stratégie de gestion des API Azure – Comment autoriser l’accès à vos points de terminaison en utilisant Google comme fournisseur de jeton OAuth.
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
ms.openlocfilehash: 7acc9071008937cd85c628878b385f1f53707e53
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70071927"
---
# <a name="authorize-access-using-google-oauth-token"></a>Autoriser l’accès à l’aide du jeton Google OAuth

Cet article montre un exemple de stratégie de Gestion des API Azure qui illustre comment autoriser l’accès à vos points de terminaison en utilisant Google comme fournisseur de jeton OAuth. Pour définir ou modifier un code de stratégie, suivez les étapes décrites dans [Définir ou modifier une stratégie](../set-edit-policies.md). Pour voir d’autres exemples, consultez [Exemples de stratégie](../policy-samples.md).

## <a name="policy"></a>Stratégie

Collez le code dans le bloc **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Simple Google OAuth validate-jwt.policy.xml)]

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les stratégies APIM :

+ [Stratégies de transformation](../api-management-transformation-policies.md)
+ [Exemples de stratégie](../policy-samples.md)

