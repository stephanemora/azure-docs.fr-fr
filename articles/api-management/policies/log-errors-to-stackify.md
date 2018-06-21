---
title: Exemple de la stratégie de gestion des API Azure - Envoyer les erreurs à Stackify à des fins de journalisation | Microsoft Docs
description: Exemple de stratégie de gestion des API Azure - Démontre comment ajouter une stratégie de journalisation des erreurs pour envoyer les erreurs à Stackify à des fins de journalisation.
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
ms.openlocfilehash: 46b6d391d6a1ee569dc27c31a0718b23a120c632
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36286716"
---
# <a name="send-errors-to-stackify-for-logging"></a>Envoyer les erreurs à Stackify à des fins de journalisation

Cet article représente un exemple de stratégie de gestion des API Azure qui explique comment ajouter une stratégie de journalisation des erreurs pour envoyer des erreurs à Stackify à des fins de journalisation. Pour définir ou modifier un code de stratégie, suivez les étapes décrites dans [Définir ou modifier une stratégie](../set-edit-policies.md). Pour voir d’autres exemples, consultez [Exemples de stratégie](../policy-samples.md).

## <a name="policy"></a>Stratégie

Collez le code dans le bloc **on-error**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Log errors to Stackify.policy.policy.xml)]

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les stratégies APIM :

+ [Stratégies de transformation](../api-management-transformation-policies.md)
+ [Exemples de stratégie](../policy-samples.md)

