---
title: Exemple de la stratégie de Gestion des API Azure - Envoyer des informations de contexte de requête au service principal | Microsoft Docs
description: Exemple de la stratégie de Gestion des API Azure - Montre comment envoyer des informations de contexte de requête au service principal.
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
ms.openlocfilehash: cc770ff700155f8ab32bbbd6737c9dad7bc6e664
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70067523"
---
# <a name="send-request-context-information-to-the-backend-service"></a>Envoyer des informations de contexte de requête au service principal

Cet article présente un exemple de la stratégie de Gestion des API Azure qui montre comment envoyer des informations de contexte de requête au service principal. Pour définir ou modifier un code de stratégie, suivez les étapes décrites dans [Définir ou modifier une stratégie](../set-edit-policies.md). Pour voir d’autres exemples, consultez [Exemples de stratégie](../policy-samples.md).

## <a name="policy"></a>Stratégie

Collez le code dans le bloc **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Send request context information to the backend service.policy.xml)]

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les stratégies APIM :

+ [Stratégies de transformation](../api-management-transformation-policies.md)
+ [Exemples de stratégie](../policy-samples.md)

