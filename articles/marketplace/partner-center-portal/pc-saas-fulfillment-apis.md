---
title: API de traitement SaaS dans Place de marché commerciale Microsoft
description: Présentation des API de traitement qui vous permettent d’intégrer vos offres SaaS dans Microsoft AppSource et Place de marché Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dsindona
ms.openlocfilehash: ba1b158bc529b148a8e3138d122c13ead19e073e
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858093"
---
# <a name="saas-fulfillment-apis-in-microsoft-commercial-marketplace"></a>API de traitement SaaS dans Place de marché commerciale Microsoft

Les API de traitement SaaS permettent aux éditeurs de logiciels indépendants (ISV) d’intégrer leurs applications SaaS dans Microsoft AppSource et Place de marché Azure. Ces API permettent aux applications d’éditeurs de logiciels indépendants d’utiliser tous les canaux commerciaux : directs, gérés par des partenaires (revendeurs) et gérés par des acteurs de terrain. Ils sont tenus de répertorier les offres SaaS prérequises dans Microsoft AppSource et Place de marché Azure.

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
