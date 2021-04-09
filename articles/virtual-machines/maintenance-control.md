---
title: Vue d’ensemble du contrôle de maintenance des machines virtuelles Azure avec le portail Azure
description: Apprenez à contrôler le moment où la maintenance est appliquée à vos machines virtuelles Azure à l'aide du contrôle de maintenance.
author: cynthn
ms.service: virtual-machines
ms.subservice: maintenance-control
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 11/19/2020
ms.author: cynthn
ms.openlocfilehash: 290a1e8da4e9b3e8eff171ab2d5837bfc9c381b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102552420"
---
# <a name="managing-platform-updates-with-maintenance-control"></a>Gestion des mises à jour de plateformes avec le contrôle de maintenance 

Gérer les mises à jour de plateforme qui ne nécessitent pas de redémarrage à l’aide du contrôle de maintenance. Azure met régulièrement à jour son infrastructure pour améliorer la fiabilité, les performances et la sécurité, ou pour lancer de nouvelles fonctionnalités. La plupart des mises à jour sont transparentes pour les utilisateurs. Certaines charges de travail sensibles, comme le gaming, le streaming multimédia et les transactions financières, ne peuvent tolérer (même quelques secondes seulement) le blocage ou la déconnexion d’une machine virtuelle pour maintenance. Le contrôle de maintenance vous donne la possibilité d’attendre les mises à jour de plateforme et de les appliquer dans une fenêtre de 35 jours consécutifs. 

Le contrôle de maintenance vous permet de décider du moment où appliquer les mises à jour sur vos machines virtuelles isolées et vos hôtes dédiés Azure.

Avec le contrôle de maintenance, vous pouvez :
- Regrouper des mises à jour dans un package de mise à jour.
- Attendre jusqu’à 35 jours avant d’appliquer les mises à jour. 
- Automatiser les mises à jour de la plateforme en configurant un calendrier de maintenance ou en utilisant [Azure Functions](https://github.com/Azure/azure-docs-powershell-samples/tree/master/maintenance-auto-scheduler).
- Les configurations de maintenance fonctionnent entre les abonnements et les groupes de ressources. 

## <a name="limitations"></a>Limites

- Les machines virtuelles doivent se trouver sur un [hôte dédié](./dedicated-hosts.md) ou être créées à l’aide d’une [taille de machine virtuelle isolée](isolation.md).
- Si une planification de maintenance est déclarée, sa durée minimale doit être de 2 heures.
- Après 35 jours, une mise à jour est automatiquement appliquée.
- L’utilisateur doit disposer d’un accès **Contributeur de ressource**.

## <a name="management-options"></a>Options de gestion

Vous pouvez créer et gérer des configurations de maintenance à l’aide de l’une des options suivantes :

- [Azure CLI](maintenance-control-cli.md)
- [Azure PowerShell](maintenance-control-powershell.md)
- [Azure portal](maintenance-control-portal.md)

Pour un exemple Azure Functions, consultez [Planification des mises à jour de maintenance avec Contrôle de maintenance et Azure Functions](https://github.com/Azure/azure-docs-powershell-samples/tree/master/maintenance-auto-scheduler).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez [Maintenance et mises à jour](maintenance-and-updates.md).
