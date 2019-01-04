---
title: Exemple de stratégie de Gestion des API Azure - Ajouter un en-tête contenant un ID de corrélation | Microsoft Docs
description: Exemple de la stratégie de Gestion des API Azure - Montre comment ajouter un en-tête contenant un ID de corrélation à la requête entrante.
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
ms.openlocfilehash: 126701674b6dc529404f5a7854cda9b31c336170
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52868427"
---
# <a name="add-a-header-containing-a-correlation-id"></a>Ajouter un en-tête contenant un ID de corrélation

Cet article montre un exemple de la stratégie de Gestion des API Azure qui illustre comment ajouter un en-tête contenant un ID de corrélation à la requête entrante. Pour définir ou modifier un code de stratégie, suivez les étapes décrites dans [Définir ou modifier une stratégie](../set-edit-policies.md). Pour voir d’autres exemples, consultez [Exemples de stratégie](../policy-samples.md).

## <a name="policy"></a>Stratégie

Collez le code dans le bloc **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Add correlation id to inbound request.policy.xml)]

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les stratégies APIM :

+ [Stratégies de transformation](../api-management-transformation-policies.md)
+ [Exemples de stratégie](../policy-samples.md)

