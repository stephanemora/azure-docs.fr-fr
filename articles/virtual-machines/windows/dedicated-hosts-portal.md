---
title: Déployer des hôtes dédiés Azure à l'aide du portail
description: Déployez des machines virtuelles sur des hôtes dédiés à l'aide du portail.
author: cynthn
ms.service: virtual-machines-windows
ms.topic: article
ms.workload: infrastructure
ms.date: 03/10/2020
ms.author: cynthn
ms.reviewer: zivr
ms.openlocfilehash: 444f7cd7094a688c43d9aedce67d293e3c32e273
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86508557"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-portal"></a>Déployer des machines virtuelles sur des hôtes dédiés à l’aide du Portail

Cet article vous guide dans la création d’un [hôte dédié](dedicated-hosts.md) Azure pour héberger vos machines virtuelles. 

[!INCLUDE [virtual-machines-common-dedicated-hosts-portal](../../../includes/virtual-machines-common-dedicated-hosts-portal.md)]

## <a name="create-a-vm"></a>Créer une machine virtuelle

1. Sélectionnez **Créer une ressource** dans le coin supérieur gauche du Portail Azure.
1. Dans la page **Nouveau** sous **Populaire**, sélectionnez **Windows Server 2016 Datacenter**.
1. Sous l’onglet **De base**, sous **Détails du projet**, vérifiez que l’abonnement approprié est sélectionné, puis sélectionnez *myDedicatedHostsRG* comme **groupe de ressources**. 
1. Sous **Détails de l’instance**, tapez *myVM* comme **Nom de la machine virtuelle**, puis choisissez *USA Est* comme **Emplacement**.
1. Dans **Options de disponibilité**, sélectionnez **Zone de disponibilité**, puis sélectionnez *1* dans la liste déroulante.
1. Pour la taille, sélectionnez **Modifier la taille**. Dans la liste des tailles disponibles, choisissez-en une dans la série Esv3, par exemple **Standard E2s v3**. Vous devrez peut-être effacer le filtre afin d’afficher toutes les tailles disponibles.
1. Sous **Compte d’administrateur**, indiquez un nom d’utilisateur (par exemple, *azureuser*) et un mot de passe. Le mot de passe doit contenir au moins 12 caractères et satisfaire aux [exigences de complexité définies](faq.md#what-are-the-password-requirements-when-creating-a-vm).
1. Sous **Règles des ports d’entrée**, choisissez **Autoriser les ports sélectionnés**, puis sélectionnez **RDP (3389)** dans la liste déroulante.
1. En haut de la page, sélectionnez l’onglet **Avancé**, puis dans la section **Hôte**, sélectionnez *myHostGroup* pour **Groupe hôte** et *myHost* pour l'**hôte**. 
    ![Sélectionner le groupe hôte et l’hôte](./media/dedicated-hosts-portal/advanced.png)
1. Conservez les valeurs par défaut restantes, puis sélectionnez le bouton **Vérifier + créer** en bas de la page.
1. Lorsque vous voyez le message indiquant que la validation a réussi, sélectionnez **Créer**.

## <a name="add-an-existing-vm"></a>Ajouter une machine virtuelle existante 

Vous pouvez ajouter une machine virtuelle existante à un hôte dédié, mais la machine virtuelle doit d’abord être arrêtée\libérée. Avant de déplacer une machine virtuelle vers un hôte dédié, vérifiez que la configuration de la machine virtuelle est prise en charge :

- La taille de la machine virtuelle doit appartenir à la même famille de tailles que l’hôte dédié. Par exemple, si votre hôte dédié est DSv3, la taille de la machine virtuelle peut être Standard_D4s_v3, mais pas Standard_A4_v2. 
- La machine virtuelle doit être située dans la même région que l’hôte dédié.
- La machine virtuelle ne peut pas faire partie d’un groupe de placements de proximité. Supprimez la machine virtuelle du groupe de placements de proximité avant de la déplacer vers un hôte dédié. Pour plus d’informations, consultez [Déplacer une machine virtuelle hors d’un groupe de placements de proximité](./proximity-placement-groups.md#move-an-existing-vm-out-of-a-proximity-placement-group).
- La machine virtuelle ne peut pas se trouver dans un groupe à haute disponibilité.
- Si la machine virtuelle se trouve dans une zone de disponibilité, il doit s’agir de la même zone de disponibilité que celle du groupe hôte. Les paramètres de zone de disponibilité de la machine virtuelle et du groupe hôte doivent correspondre.

Déplacez la machine virtuelle vers un hôte dédié à l’aide du [portail](https://portal.azure.com).

1. Ouvrez la page de la machine virtuelle.
1. Sélectionnez **Arrêter** pour arrêter\libérer la machine virtuelle.
1. Sélectionnez **Configuration** dans le menu de gauche.
1. Sélectionnez un groupe hôte et un hôte dans les menus déroulants.
1. Lorsque vous avez terminé, sélectionnez **Enregistrer** en haut de la page.
1. Une fois la machine virtuelle ajoutée à l’hôte, sélectionnez **Vue d’ensemble** dans le menu de gauche.
1. En haut de la page, sélectionnez **Démarrer** pour redémarrer la machine virtuelle.

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus, voir la page de présentation [Hôtes dédiés](dedicated-hosts.md). 

- Un exemple de modèle, disponible [ici](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md), utilise les zones et les domaines d’erreur pour offrir une résilience maximale dans une région.

- Vous pouvez également déployer un hôte dédié à l’aide d’[Azure PowerShell](dedicated-hosts-powershell.md).
