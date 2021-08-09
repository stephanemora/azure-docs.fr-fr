---
title: Contrôle de maintenance pour les mises à niveau d’image de système d’exploitation sur des groupes de machines virtuelles identiques Azure à l’aide du portail Azure
description: Découvrez comment contrôler le moment où les mises à niveau automatiques des images de système d’exploitation sont déployées sur vos groupes de machines virtuelles identiques Azure à l’aide du contrôle de maintenance et du portail Azure.
author: ju-shim
ms.service: virtual-machine-scale-sets
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 06/01/2021
ms.author: jushiman
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6537728feb2145520ee49457b00d9944d5967c9f
ms.sourcegitcommit: 8651d19fca8c5f709cbb22bfcbe2fd4a1c8e429f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112073032"
---
# <a name="maintenance-control-for-os-image-upgrades-on-azure-virtual-machine-scale-sets-using-azure-portal"></a>Contrôle de maintenance pour les mises à niveau d’image de système d’exploitation sur des groupes de machines virtuelles identiques Azure à l’aide du portail Azure

Le contrôle de maintenance vous permet de choisir quand appliquer automatiquement les mises à niveau d’images de système d’exploitation invité à vos groupes de machines virtuelles identiques. Cette rubrique traite des options du portail Azure pour le contrôle de la maintenance. Pour plus d’informations sur l’utilisation du contrôle de maintenance, consultez [Contrôle maintenance pour les groupes de machines virtuelles identiques Azure](virtual-machine-scale-sets-maintenance-control.md).


## <a name="create-a-maintenance-configuration"></a>Créer une configuration de maintenance

1. Connectez-vous au portail Azure.

1. Rechercher des **configurations de maintenance**.
    
    :::image type="content" source="media/virtual-machine-scale-sets-maintenance-control-portal/maintenance-configurations-search-bar.png" alt-text="Capture d’écran montrant comment ouvrir les configurations de maintenance":::

1. Sélectionnez **Ajouter**.

    :::image type="content" source="media/virtual-machine-scale-sets-maintenance-control-portal/maintenance-configurations-add.png" alt-text="Capture d’écran montrant comment ajouter une configuration de maintenance":::

1. Dans l’onglet Informations de base, choisissez un abonnement et un groupe de ressources, fournissez un nom pour la configuration, choisissez une région, puis sélectionnez *Mise à niveau de l’image du système d’exploitation* en guise d’étendue. Sélectionnez **Suivant**.
    
    :::image type="content" source="media/virtual-machine-scale-sets-maintenance-control-portal/maintenance-configurations-basics-tab.png" alt-text="Capture d’écran montrant les bases de la configuration de maintenance":::

1. Sous l’onglet Planification, déclarez une fenêtre planifiée au cours de laquelle Azure appliquera les mises à jour à vos ressources. Définissez une date de début, une fenêtre de maintenance et une périodicité. Une fois que vous aurez créé une fenêtre planifiée, vous n’avez plus besoin d’appliquer les mises à jour manuellement. Sélectionnez **Suivant**. 

    > [!IMPORTANT]
    > La **durée** de la fenêtre de maintenance doit être d’au moins *5 heures*. La **périodicité** de la maintenance doit être configurée de façon à ce que la maintenance soit répétée au moins une fois par jour. 

    :::image type="content" source="media/virtual-machine-scale-sets-maintenance-control-portal/maintenance-configurations-schedule-tab.png" alt-text="Capture d’écran montrant la planification de la configuration de la maintenance":::

1. Sous l’onglet Affectation, affectez des ressources dès maintenant. Vous pouvez également reporter cette étape après le déploiement de la configuration de la maintenance. Sélectionnez **Suivant**.

1. Ajoutez des balises et des valeurs. Sélectionnez **Suivant**.
    
    :::image type="content" source="media/virtual-machine-scale-sets-maintenance-control-portal/maintenance-configurations-tags-tab.png" alt-text="Capture d’écran montrant comment ajouter des balises à la configuration de maintenance":::

1. Passez en revue le résumé. Sélectionnez **Create** (Créer).

1. Une fois le déploiement effectué, sélectionnez **Accéder à la ressource**.


## <a name="assign-the-configuration"></a>Affecter la configuration

Dans la page des détails de configuration de la maintenance, sélectionnez **Affectations**, puis **Affecter une ressource**. 

![Capture d’écran montrant comment affecter une ressource](media/virtual-machine-scale-sets-maintenance-control-portal/maintenance-configurations-add-assignment.png)

Sélectionnez les ressources auxquelles vous souhaitez appliquer la configuration de la maintenance, puis choisissez **OK**. La colonne **Type** indique si la ressource est une machine virtuelle isolée ou un hôte dédié Azure. La machine virtuelle doit être en cours d’exécution pour que l’on puisse affecter la configuration. Une erreur se produit si vous tentez d’affecter une configuration à une machine virtuelle arrêtée. 


## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Découvrir la maintenance et les mises à jour pour les machines virtuelles s’exécutant dans Azure](maintenance-and-updates.md)
