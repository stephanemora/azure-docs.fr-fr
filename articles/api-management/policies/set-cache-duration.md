---
title: Exemple de la stratégie de gestion des API Azure – Définir la durée du cache de réponse | Microsoft Docs
description: Exemple de stratégie de gestion des API Azure – Comment définir la durée du cache de réponse à l’aide de la valeur maxAge dans l’en-tête Cache-Control envoyé par le serveur principal...
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
ms.openlocfilehash: 67d2f334e2088d96543fabd3b99cac71d95630eb
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70071968"
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

