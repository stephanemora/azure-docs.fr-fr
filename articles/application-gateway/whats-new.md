---
title: Nouveautés d’Azure Application Gateway
description: Découvrez les nouveautés d’Azure Application Gateway, notamment les dernières notes de publication, les problèmes connus, les corrections de bogues, les fonctionnalités dépréciées et les changements à venir.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.date: 4/1/2019
ms.author: victorh
ms.openlocfilehash: f686c8ac53db2d128cf5bb20f252c547348e5ac7
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/02/2019
ms.locfileid: "58863098"
---
# <a name="whats-new-in-azure-application-gateway"></a>Nouveautés d’Azure Application Gateway

Azure Application Gateway est mis à jour en continu. Pour vous informer des développements les plus récents, cet article détaille les thèmes suivants :

- Versions les plus récentes
- Problèmes connus
- Résolution des bogues
- Fonctionnalités dépréciées

## <a name="new-features"></a>Nouvelles fonctionnalités

|Fonctionnalité  |Description  |Date de l’ajout  |
|---------|---------|---------|
|Opérations de réécriture et CRUD sur les en-têtes     |Vous pouvez désormais réécrire les en-têtes HTTP. Consultez le [tutoriel : Créer une passerelle d’application et réécrire des en-têtes HTTP](tutorial-http-header-rewrite-powershell.md) pour plus d’informations.|Décembre 2018|
|Configuration de WAF et liste d’exclusions     |Nous avons ajouté des options pour vous aider à configurer votre WAF et à réduire les faux positifs. Consultez [Limites de la taille des demandes adressées au pare-feu d’application web et listes d’exclusions](application-gateway-waf-configuration.md) pour plus d’informations.|Décembre 2018|
|Préversion de la mise à l’échelle automatique, de la redondance de zone et de la prise en charge des adresses IP virtuelles statiques     |La référence SKU v2 apporte de nombreuses améliorations, dont la mise à l’échelle automatique et l’accroissement des performances. Consultez [Nouveautés d’Azure Application Gateway](overview.md#autoscaling-public-preview) pour plus d’informations.|Septembre 2018|
|Vidage des connexions     |Le vidage des connexions vous permet de supprimer correctement des membres de vos pools de back-ends. Pour plus d’informations, consultez [Vidage des connexions](overview.md#connection-draining).|Septembre 2018|
|Pages d’erreur personnalisées     |Avec les pages d’erreurs personnalisées, vous pouvez créer une page d’erreur dans le format du reste de vos sites web. Pour y parvenir, consultez [Créer des pages d’erreur personnalisées Application Gateway](custom-error.md).|Septembre 2018|
|Améliorations des métriques     |Vous pouvez obtenir une meilleure vue de l’état de votre passerelle Application Gateway avec des métriques améliorées. Pour activer les métriques sur votre passerelle Application Gateway, consultez [Intégrité du serveur principal, journaux de diagnostic et métriques pour la passerelle Application Gateway](application-gateway-diagnostics.md).|Juin 2018|

## <a name="known-issues"></a>Problèmes connus

- [Problèmes connus dans la référence SKU v2](application-gateway-autoscaling-zone-redundant.md#known-issues-and-limitations)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure Application Gateway, consultez [Présentation d’Azure Application Gateway](overview.md).