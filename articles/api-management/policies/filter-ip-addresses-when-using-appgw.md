---
title: Exemple de stratégie de gestion des API - Filtrer sur l’adresse IP lors de l’utilisation d’Application Gateway
titleSuffix: Azure API Management
description: Exemple de stratégie de gestion des API Azure - Montre comment filtrer sur l’adresse IP de demande lors de l’utilisation d’une passerelle Application Gateway.
services: api-management
documentationcenter: ''
author: jftl6y
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/13/2020
ms.author: joscot
ms.custom: fasttrack-new
ms.openlocfilehash: 45e16c9aa9e4b04e7225320951e9f839fae75ba3
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/14/2020
ms.locfileid: "75942362"
---
# <a name="filter-on-request-ip-address-when-using-an-application-gateway"></a>Filtrer sur l’adresse IP de demande lors de l’utilisation d’une passerelle Application Gateway

Cet article présente un exemple de stratégie de gestion des API Azure qui montre comment filtrer sur l’adresse IP de demande quand l’instance Gestion des API est accessible par le biais d’une passerelle Application Gateway ou d’un autre intermédiaire. Pour définir ou modifier un code de stratégie, suivez les étapes décrites dans [Définir ou modifier une stratégie](../set-edit-policies.md). Pour voir d’autres exemples, consultez [Exemples de stratégie](../policy-samples.md).

## <a name="policy"></a>Stratégie

Collez le code dans le bloc **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Filter on IP Address when using Application Gateway.policy.xml)]

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les stratégies APIM :

+ [Politiques de restrictions d’accès](../api-management-access-restriction-policies.md)
+ [Exemples de stratégie](../policy-samples.md)
