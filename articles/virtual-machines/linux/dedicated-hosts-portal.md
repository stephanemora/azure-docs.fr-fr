---
title: Déployer des hôtes dédiés Azure à l’aide du portail Azure
description: Déployez des machines virtuelles sur des hôtes dédiés à l’aide du Portail Azure.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure
ms.date: 03/10/2020
ms.author: cynthn
ms.openlocfilehash: 195a19ef881f235ad8e42f23b53da9e667ef88d0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79086764"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-portal"></a>Déployer des machines virtuelles sur des hôtes dédiés à l’aide du Portail

Cet article vous guide dans la création d’un [hôte dédié](dedicated-hosts.md) Azure pour héberger vos machines virtuelles. 

[!INCLUDE [virtual-machines-common-dedicated-hosts-portal](../../../includes/virtual-machines-common-dedicated-hosts-portal.md)]

## <a name="create-a-vm"></a>Créer une machine virtuelle

1. Choisissez **Créer une ressource** en haut à gauche du Portail Azure.
1. Dans la zone de recherche au-dessus de la liste des ressources de la Place de marché Azure, recherchez et sélectionnez **Ubuntu Server 16.04 LTS** de Canonical, puis choisissez **Créer**.
1. Sous l’onglet **De base**, sous **Détails du projet**, vérifiez que l’abonnement approprié est sélectionné, puis sélectionnez *myDedicatedHostsRG* comme **groupe de ressources**. 
1. Sous **Détails de l’instance**, tapez *myVM* comme **Nom de la machine virtuelle**, puis choisissez *USA Est* comme **Emplacement**.
1. Dans **Options de disponibilité**, sélectionnez **Zone de disponibilité**, puis sélectionnez *1* dans la liste déroulante.
1. Pour la taille, sélectionnez **Modifier la taille**. Dans la liste des tailles disponibles, choisissez-en une dans la série Esv3, par exemple **Standard E2s v3**. Vous devrez peut-être effacer le filtre afin d’afficher toutes les tailles disponibles.
1. Sous **Compte d’administrateur**, sélectionnez **Clé publique SSH**, tapez votre nom d’utilisateur, puis collez votre clé publique dans la zone de texte. Supprimez les espaces blancs au début ou à la fin de votre clé publique.

    ![Compte d’administrateur](./media/quick-create-portal/administrator-account.png)

1. Sous **Règles des ports d’entrée** > **Ports d’entrée publics**, choisissez **Autoriser les ports sélectionnés**, puis sélectionnez **SSH (22)** dans la liste déroulante. 
1. En haut de la page, sélectionnez l’onglet **Avancé**, puis dans la section **Hôte**, sélectionnez *myHostGroup* pour **Groupe hôte** et *myHost* pour l'**hôte**. 
    ![Sélectionner le groupe hôte et l’hôte](./media/dedicated-hosts-portal/advanced.png)
1. Conservez les valeurs par défaut restantes, puis sélectionnez le bouton **Vérifier + créer** en bas de la page.
1. Lorsque vous voyez le message indiquant que la validation a réussi, sélectionnez **Créer**.

Quelques minutes sont nécessaires pour le déploiement de votre machine virtuelle.

## <a name="add-an-existing-vm"></a>Ajouter une machine virtuelle existante 

Vous pouvez ajouter une machine virtuelle existante à un hôte dédié, mais la machine virtuelle doit d’abord être arrêtée\libérée. Avant de déplacer une machine virtuelle vers un hôte dédié, vérifiez que la configuration de la machine virtuelle est prise en charge :

- La taille de la machine virtuelle doit appartenir à la même famille de tailles que l’hôte dédié. Par exemple, si votre hôte dédié est DSv3, la taille de la machine virtuelle peut être Standard_D4s_v3, mais pas Standard_A4_v2. 
- La machine virtuelle doit être située dans la même région que l’hôte dédié.
- La machine virtuelle ne peut pas faire partie d’un groupe de placements de proximité. Supprimez la machine virtuelle du groupe de placements de proximité avant de la déplacer vers un hôte dédié. Pour plus d’informations, consultez [Déplacer une machine virtuelle hors d’un groupe de placements de proximité](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups#move-an-existing-vm-out-of-a-proximity-placement-group).
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

- Vous pouvez également déployer un hôte dédié à l’aide de l’[interface de ligne de commande Azure](dedicated-hosts-cli.md).



