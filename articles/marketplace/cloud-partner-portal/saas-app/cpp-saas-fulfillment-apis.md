---
title: API de traitement des commandes SaaS - place de marché Azure | Microsoft Docs
description: Présente les versions de l’exécution avec la place de marché Azure propose des API qui vous permettent d’intégrer votre SaaS.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: pbutlerm
ms.openlocfilehash: c7da46984d592abc6ed97d7490fde732bf26b0ba
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59798728"
---
# <a name="saas-fulfillment-apis"></a>API de traitement SaaS

Les API de traitement des commandes SaaS permettent des éditeurs de logiciels indépendants (ISV) pour intégrer leurs applications SaaS à la place de marché Azure. Ces API permettre aux applications d’éditeurs de logiciels indépendants à participer à tous les canaux de commerce électronique : direct, par les partenaires (revendeurs) et conduit de champ.  Ils sont nécessaires pour répertorier les offres de SaaS transactable sur la place de marché Azure.

> [!WARNING]
> La version actuelle de cette API est la Version 2, qui doivent être utilisées pour tous les SaaS nouvelle offre.  La version 1 de l’API est déconseillée et est conservée pour prendre en charge des offres existantes.


## <a name="business-model-support"></a>Prise en charge du modèle métier

Cette API prend en charge les fonctions de modèle suivant de l’entreprise ; Vous pouvez :

* Spécifiez plusieurs plans pour une offre. Ces plans offrent des fonctionnalités différentes et peuvent être facturées différemment.
* Fournir une offre sur une par site ou un par un utilisateur de base du modèle de facturation.
* Fournir mensuelle et annuelle (paiement immédiat) options de facturation.
* Proposer des tarifs privée à un client basé sur un contrat entreprise négocié.


## <a name="next-steps"></a>Étapes suivantes

Si vous ne le n'avez pas déjà fait, inscrivez votre application SaaS dans le [Azure portal](https://ms.portal.azure.com) comme expliqué dans [inscrire une Application Azure AD](./cpp-saas-registration.md).  Ensuite, utilisez la version la plus récente de cette interface pour le développement : [Version d’API SaaS Fulfillment 2](./cpp-saas-fulfillment-api-v2.md).
