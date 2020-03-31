---
title: API de traitement SaaS | Place de marché Azure
description: Présente les versions des API de traitement qui vous permettent d’intégrer vos offres SaaS avec la Place de marché Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dsindona
ms.openlocfilehash: 92b1c52457fa92709381124480c05a5f636167f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275728"
---
# <a name="saas-fulfillment-apis"></a>API de traitement SaaS

Les API de traitement SaaS permettent aux éditeurs de logiciels indépendants (ISV) d’intégrer leurs applications SaaS avec la Place de marché Azure. Ces API permettent aux applications d’éditeurs de logiciels indépendants d’utiliser tous les canaux commerciaux : directs, gérés par des partenaires (revendeurs) et gérés par des acteurs de terrain.  Elles sont obligatoires pour répertorier les offres SaaS négociables sur la Place de marché Azure.

> [!WARNING]
> La version actuelle de cette API (version 2) doit être utilisée pour toutes les nouvelles offres SaaS.  La version 1 de l’API est déconseillée. Elle est conservée pour prendre en charge des offres existantes.


## <a name="business-model-support"></a>Prise en charge du modèle commercial

Cette API prend en charge les fonctionnalités suivantes du modèle commercial :

* Spécifier plusieurs plans pour une offre. Ces plans incluant des fonctionnalités distinctes peuvent être facturés différemment.
* Proposer une offre basée sur un modèle de facturation par site ou par utilisateur.
* Proposer des options de facturation mensuelle et annuelle (paiement anticipé).
* Proposer un tarif privé à un client sur la base d’un contrat entreprise négocié.


## <a name="next-steps"></a>Étapes suivantes

Si ce n’est déjà fait, inscrivez votre application SaaS via le [portail Azure](https://ms.portal.azure.com), comme expliqué dans [Inscrire une application Azure AD](./pc-saas-registration.md).  Ensuite, utilisez la version la plus récente de cette interface pour le développement : [API de traitement SaaS version 2](./pc-saas-fulfillment-api-v2.md).
