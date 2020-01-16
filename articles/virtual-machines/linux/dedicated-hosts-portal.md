---
title: Déployer des hôtes dédiés Azure à l’aide du portail Azure
description: Déployez des machines virtuelles sur des hôtes dédiés à l’aide du Portail Azure.
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/09/2020
ms.author: cynthn
ms.openlocfilehash: c8e2ac929b3285b0ba122928485b423e34dc8f4f
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2020
ms.locfileid: "75835132"
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

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus, voir la page de présentation [Hôtes dédiés](dedicated-hosts.md).

- Un exemple de modèle, disponible [ici](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md), utilise les zones et les domaines d’erreur pour offrir une résilience maximale dans une région.

- Vous pouvez également déployer un hôte dédié à l’aide de l’[interface de ligne de commande Azure](dedicated-hosts-cli.md).



