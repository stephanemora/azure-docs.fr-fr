---
title: Exemple de la stratégie de Gestion des API Azure - Définir la durée du cache de réponse | Microsoft Docs
description: Exemple de stratégie de Gestion des API Azure - Montre comment définir la durée du cache de réponse à l’aide de la valeur maxAge dans l’en-tête Cache-Control envoyé par le serveur principal...
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
ms.openlocfilehash: 8f7126f5cd6bf6f142c603e4b1baee4a6c20dea2
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36287549"
---
# <a name="set-response-cache-duration"></a>Définir la durée du cache de réponse

Cet article montre un exemple de stratégie de Gestion des API Azure qui illustre comment définir la durée du cache de réponse à l’aide de la valeur maxAge dans l’en-tête Cache-Control envoyé par le serveur principal. Pour définir ou modifier un code de stratégie, suivez les étapes décrites dans [Définir ou modifier une stratégie](../set-edit-policies.md). Pour voir d’autres exemples, consultez [Exemples de stratégie](../policy-samples.md).

## <a name="policy"></a>Stratégie

Collez le code dans le bloc **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Set cache duration using response cache control header.policy.xml)]

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les stratégies APIM :

+ [Stratégies de transformation](../api-management-transformation-policies.md)
+ [Exemples de stratégie](../policy-samples.md)

