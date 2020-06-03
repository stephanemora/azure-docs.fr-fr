---
title: Nouveautés du pare-feu d’applications web Azure
description: Découvrez les nouveautés du pare-feu d'applications web Azure, notamment les dernières notes de publication, les problèmes connus, les corrections de bogues, les fonctionnalités déconseillées et les changements à venir.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: overview
ms.date: 10/24/2019
ms.author: victorh
ms.openlocfilehash: 11212c931e38b1c6319c7c3554155d9d17a1080c
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83745142"
---
# <a name="whats-new-in-azure-web-application-firewall"></a>Nouveautés du pare-feu d’applications web Azure

Le pare-feu d’applications web Azure est mis à jour de manière continue. Pour vous informer des développements les plus récents, cet article détaille les thèmes suivants :

- Versions les plus récentes
- Problèmes connus
- Résolution des bogues
- Fonctionnalités dépréciées

## <a name="new-features"></a>Nouvelles fonctionnalités

|Fonctionnalité  |Description  |Date de l’ajout  |
|---------|---------|---------|
|Ensemble de règles d’atténuation des robots (version préliminaire)|Vous pouvez activer un ensemble de règles d’atténuation de robot, en plus de l’ensemble de règles CRS que vous choisissez. | Novembre 2019 |
|Intégration de GeoDB (version préliminaire)|Il est maintenant possible de créer des règles personnalisées qui limitent le trafic par pays/région d’origine. | Novembre 2019 |
|WAF par site/stratégie par URI (version préliminaire)|WAF-v2 prend désormais en charge l’application d’une stratégie aux écouteurs, ainsi que les règles basées sur le chemin d’accès. Consultez [Créer une stratégie de pare-feu d’applications web (WAF)](create-waf-policy-ag.md). | Novembre 2019 |
|Règles personnalisées WAF |Application Gateway WAF_v2 prend désormais en charge la création de règles personnalisées. Consultez [Règles personnalisées Application Gateway](custom-waf-rules-overview.md). |Juin 2019 |
|Configuration de WAF et liste d’exclusions     |Nous avons ajouté des options pour vous aider à configurer votre WAF et à réduire les faux positifs. Consultez [Limites de la taille des demandes adressées au pare-feu d’application web et listes d’exclusions](application-gateway-waf-configuration.md) pour plus d’informations.|Décembre 2018|

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le pare-feu d’applications Web sur Application Gateway, consultez [Pare-feu d’applications web Azure sur Application Gateway](ag-overview.md).
