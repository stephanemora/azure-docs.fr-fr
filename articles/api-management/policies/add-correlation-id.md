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
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: 9f091345a4eaf174c47959cad3cb4525fd926689
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70074207"
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

