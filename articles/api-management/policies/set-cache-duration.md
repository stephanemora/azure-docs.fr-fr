---
title: Exemple de stratégie de gestion des API - Définir la durée du cache de réponse
titleSuffix: Azure API Management
description: Exemple de stratégie de gestion des API Azure – Comment définir la durée du cache de réponse à l’aide de la valeur maxAge dans l’en-tête Cache-Control envoyé par le serveur principal...
services: api-management
documentationcenter: ''
author: dlepow
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/13/2017
ms.author: danlep
ms.openlocfilehash: 512310da23b4157bfcd732fdf429bdc2243d630c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128651956"
---
# <a name="set-response-cache-duration"></a>Définir la durée du cache de réponse

Cet article montre un exemple de stratégie de Gestion des API Azure qui illustre comment définir la durée du cache de réponse à l’aide de la valeur maxAge dans l’en-tête Cache-Control envoyé par le serveur principal. Pour définir ou modifier un code de stratégie, suivez les étapes décrites dans [Définir ou modifier une stratégie](../set-edit-policies.md). Pour voir d’autres exemples, consultez [Exemples de stratégie](../policy-reference.md).

## <a name="policy"></a>Policy

Collez le code dans le bloc **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Set cache duration using response cache control header.policy.xml)]

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les stratégies APIM :

+ [Stratégies de transformation](../api-management-transformation-policies.md)
+ [Exemples de stratégie](../policy-reference.md)