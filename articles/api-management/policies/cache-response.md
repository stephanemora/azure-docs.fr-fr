---
title: Exemple de la stratégie de Gestion des API Azure - Ajouter des fonctionnalités à un service principal | Microsoft Docs
description: Exemple de la stratégie de Gestion des API Azure - Montre comment ajouter des fonctionnalités à un service principal. Par exemple, accepter un nom d’emplacement au lieu des latitude et longitude dans une API de prévisions météorologiques.
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
ms.openlocfilehash: 7c9edbf4b2d231453cd336521a04ba6b7714b696
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61062205"
---
# <a name="add-capabilities-to-a-backend-service"></a>Ajouter des fonctionnalités à un service principal

Cet article présente un exemple de la stratégie de Gestion des API Azure qui montre comment ajouter des fonctionnalités à un service principal. Par exemple, accepter un nom d’emplacement au lieu des latitude et longitude dans une API de prévisions météorologiques. Pour définir ou modifier un code de stratégie, suivez les étapes décrites dans [Définir ou modifier une stratégie](../set-edit-policies.md). Pour voir d’autres exemples, consultez [Exemples de stratégie](../policy-samples.md).

## <a name="policy"></a>Stratégie

Collez le code dans le bloc **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Call out to an HTTP endpoint and cache the response.policy.xml)]

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les stratégies APIM :

+ [Stratégies de transformation](../api-management-transformation-policies.md)
+ [Exemples de stratégie](../policy-samples.md)

