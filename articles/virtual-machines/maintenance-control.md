---
title: Vue d’ensemble du contrôle de maintenance des machines virtuelles Azure avec le portail Azure
description: Apprenez à contrôler le moment où la maintenance est appliquée à vos machines virtuelles Azure à l'aide du contrôle de maintenance.
author: cynthn
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 04/22/2020
ms.author: cynthn
ms.openlocfilehash: 950f4cfda73b40db0de8ba035868573cda1a5017
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84675793"
---
# <a name="managing-platform-updates-with-maintenance-control"></a>Gestion des mises à jour de plateformes avec le contrôle de maintenance 

Gérer les mises à jour de plateforme qui ne nécessitent pas de redémarrage à l’aide du contrôle de maintenance. Azure met régulièrement à jour son infrastructure pour améliorer la fiabilité, les performances et la sécurité, ou pour lancer de nouvelles fonctionnalités. La plupart des mises à jour sont transparentes pour les utilisateurs. Certaines charges de travail sensibles, comme le gaming, le streaming multimédia et les transactions financières, ne peuvent tolérer (même quelques secondes seulement) le blocage ou la déconnexion d’une machine virtuelle pour maintenance. Le contrôle de maintenance vous donne la possibilité d’attendre les mises à jour de plateforme et de les appliquer dans une fenêtre de 35 jours consécutifs. 

Le contrôle de maintenance vous permet de décider du moment où appliquer les mises à jour sur vos machines virtuelles isolées et vos hôtes dédiés Azure.

Avec le contrôle de maintenance, vous pouvez :
- Regrouper des mises à jour dans un package de mise à jour.
- Attendre jusqu’à 35 jours avant d’appliquer les mises à jour. 
- Automatiser les mises à jour de plateforme pour votre fenêtre de maintenance avec Azure Functions.
- Les configurations de maintenance fonctionnent entre les abonnements et les groupes de ressources. 

## <a name="limitations"></a>Limites

- Les machines virtuelles doivent se trouver sur un [hôte dédié](./linux/dedicated-hosts.md) ou être créées à l’aide d’une [taille de machine virtuelle isolée](./linux/isolation.md).
- Après 35 jours, une mise à jour est automatiquement appliquée.
- L’utilisateur doit disposer d’un accès **Contributeur de ressource**.

## <a name="management-options"></a>Options de gestion

Vous pouvez créer et gérer des configurations de maintenance à l’aide de l’une des options suivantes :

- [Azure CLI](maintenance-control-cli.md)
- [Azure PowerShell](maintenance-control-powershell.md)
- [Azure portal](maintenance-control-portal.md)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez [Maintenance et mises à jour](maintenance-and-updates.md).
