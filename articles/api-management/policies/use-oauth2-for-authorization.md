---
title: Exemple de la stratégie de Gestion des API Azure - Utiliser OAuth2 pour l’autorisation entre la passerelle et un serveur principal | Microsoft Docs
description: Exemple de la stratégie de Gestion des API Azure - Montre comment utiliser OAuth2 pour l’autorisation entre la passerelle et un serveur principal. Il montre comment obtenir un jeton d’accès d’AAD et le transférer au serveur principal.
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
ms.openlocfilehash: d064e918d514b9be1b9fa2dbf30c10edf5167908
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36287818"
---
# <a name="use-oauth2-for-authorization-between-the-gateway-and-a-backend"></a>Utiliser OAuth2 pour l’autorisation entre la passerelle et un serveur principal

Cet article présente un exemple de la stratégie de Gestion des API Azure qui montre comment utiliser OAuth2 pour l’autorisation entre la passerelle et un serveur principal. Il montre comment obtenir un jeton d’accès d’AAD et le transférer au serveur principal. 

Pour définir ou modifier un code de stratégie, suivez les étapes décrites dans [Définir ou modifier une stratégie](../set-edit-policies.md). Pour voir d’autres exemples, consultez [Exemples de stratégie](../policy-samples.md).

Le script suivant utilise les propriétés qui apparaissent dans {{property}}. Pour plus d’informations sur les propriétés et leur utilisation dans les stratégies Gestion des API, consultez [cette](../api-management-howto-properties.md) rubrique.
 
## <a name="policy"></a>Stratégie

Collez le code dans le bloc **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Get OAuth2 access token from AAD and forward it to the backend.policy.xml)]
  
## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les stratégies APIM :

+ [Stratégies de transformation](../api-management-transformation-policies.md)
+ [Exemples de stratégie](../policy-samples.md)

