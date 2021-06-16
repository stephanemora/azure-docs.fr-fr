---
title: Vue d’ensemble du contrôle de maintenance pour les mises à niveau d’images de système d’exploitation sur les groupes de machines virtuelles identiques Azure
description: Découvrez comment contrôler le moment où les mises à niveau automatiques des images de système d’exploitation sont déployées sur vos groupes de machines virtuelles identiques Azure à l’aide du contrôle de maintenance.
author: ju-shim
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/11/2020
ms.author: jushiman
ms.openlocfilehash: c97294833ea04e4af559008cf55d3aef7230ef81
ms.sourcegitcommit: 8651d19fca8c5f709cbb22bfcbe2fd4a1c8e429f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112072232"
---
# <a name="maintenance-control-for-azure-virtual-machine-scale-sets"></a>Contrôle de maintenance pour les groupes de machines virtuelles identiques Azure 

Gérez les [mises à niveau automatiques des images de système d’exploitation](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md) pour vos groupes de machines virtuelles identiques à l’aide du contrôle de maintenance.

Le contrôle de maintenance vous permet de décider quand appliquer des mises à jour aux disques de système d’exploitation dans vos groupes de machines virtuelles identiques, avec une expérience plus facile et plus prévisible. 

Les configurations de maintenance fonctionnent entre les abonnements et les groupes de ressources.

Le workflow complet se résume aux étapes suivantes : 
- Créez une configuration de maintenance.
- Associez un groupe de machines virtuelles identiques à une configuration de maintenance.
- Activez les mises à niveau automatiques des systèmes d’exploitation.


## <a name="limitations"></a>Limites

- Les machines virtuelles doivent se trouver dans un groupe identique.
- L’utilisateur doit disposer d’un accès **Contributeur de ressource**.
- La durée de la maintenance doit être de 5 heures ou plus dans la configuration de la maintenance.
- La périodicité de la maintenance doit être définie sur « jour » dans la configuration de la maintenance


## <a name="management-options"></a>Options de gestion

Vous pouvez créer et gérer des configurations de maintenance à l’aide de l’une des options suivantes :

- [Azure PowerShell](virtual-machine-scale-sets-maintenance-control-powershell.md)
- [Azure CLI](virtual-machine-scale-sets-maintenance-control-cli.md)
- [Azure portal](virtual-machine-scale-sets-maintenance-control-portal.md)


## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Contrôle de maintenance d’un groupe de machines virtuelles identiques avec PowerShell](virtual-machine-scale-sets-maintenance-control-powershell.md)
