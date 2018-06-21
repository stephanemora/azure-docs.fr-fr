---
title: Exemple de stratégie de Gestion des API Azure - Autoriser l’accès à l’aide du jeton Google OAuth | Microsoft Docs
description: Exemple de stratégie de Gestion des API Azure - Montre comment autoriser l’accès à vos points de terminaison en utilisant Google comme fournisseur de jeton OAuth.
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
ms.openlocfilehash: 22aed976ef69288aa0e49215a739174786843527
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36284584"
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

