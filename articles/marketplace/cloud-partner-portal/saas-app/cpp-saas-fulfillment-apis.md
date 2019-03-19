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
ms.date: 02/27/2019
ms.author: pbutlerm
ms.openlocfilehash: 9c3fbe7cd7ebd41f59be360f40d66b8d38dbce5e
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2019
ms.locfileid: "57319274"
---
# <a name="saas-fulfillment-apis"></a>API de traitement des commandes de SaaS

L’API de traitement des commandes SaaS permet des éditeurs de logiciels indépendants (ISV) pour intégrer leurs applications SaaS à la place de marché Azure. Cette API permet aux applications ISV participer à tous les canaux de commerce électronique : direct, par les partenaires (revendeurs) et conduit de champ.  Cette API est une exigence pour la liste que transactable SaaS offre sur la place de marché Azure.

La version actuelle de cette API est la Version 2, qui doivent être utilisées pour tous les SaaS nouvelle offre.  La version 1 de l’API est déconseillée et est conservée pour prendre en charge des offres existantes.


## <a name="business-model-support"></a>Prise en charge du modèle métier

Cette API prend en charge les fonctions de modèle suivant de l’entreprise ; Vous pouvez :

* Spécifiez plusieurs plans pour une offre. Ces plans offrent des fonctionnalités différentes et peuvent être facturées différemment.
* Fournir une offre sur une par site ou un par un utilisateur de base du modèle de facturation.
* Fournir mensuelle et annuelle (paiement immédiat) options de facturation.
* Proposer des tarifs privée à un client basé sur un contrat entreprise négocié.


## <a name="next-steps"></a>Étapes suivantes

Utilisez la version la plus récente de cette interface pour le développement : [Version d’API SaaS Fulfillment 2](./cpp-saas-fulfillment-api-v2.md).
