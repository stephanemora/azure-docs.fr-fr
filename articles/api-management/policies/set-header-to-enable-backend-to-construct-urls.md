---
title: 'Exemple de stratégie de gestion des API Azure : ajouter un en-tête Forwarded | Microsoft Docs'
description: 'Exemple de stratégie de gestion des API Azure : montre comment ajouter un en-tête Forwarded à la demande entrante pour autoriser l’API de service principal à concevoir des URL correctes.'
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
ms.openlocfilehash: a9dfcbf4be4b659d761d66d67d2ae4c7b70a245e
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36284162"
---
# <a name="add-a-forwarded-header"></a>Ajouter un en-tête Forwarded

Cet article présente un exemple de stratégie de gestion des API Azure qui montre comment ajouter un en-tête Forwarded à la demande entrante pour autoriser l’API de service principal à concevoir des URL correctes. Pour définir ou modifier un code de stratégie, suivez les étapes décrites dans [Définir ou modifier une stratégie](../set-edit-policies.md). Pour voir d’autres exemples, consultez [Exemples de stratégie](../policy-samples.md).

## <a name="code"></a>Code

Collez le code dans le bloc **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Forward gateway hostname to backend for generating correct urls in responses.policy.xml)]

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les stratégies APIM :

+ [Stratégies de transformation](../api-management-transformation-policies.md)
+ [Exemples de stratégie](../policy-samples.md)
