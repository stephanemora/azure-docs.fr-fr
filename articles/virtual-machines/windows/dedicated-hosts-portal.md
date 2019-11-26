---
title: Déployer des hôtes dédiés Azure à l’aide du Portail Azure
description: Déployez des machines virtuelles sur des hôtes dédiés à l’aide du Portail Azure.
services: virtual-machines-windows
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/26/2019
ms.author: cynthn
ms.openlocfilehash: 7abfa00b01f2e5e32b2486d69f36ac62f844c5ad
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033832"
---
# <a name="preview-deploy-vms-to-dedicated-hosts-using-the-portal"></a>Aperçu : Déployer des machines virtuelles sur des hôtes dédiés à l’aide du Portail

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


## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations, consultez la page de présentation [Hôtes dédiés](dedicated-hosts.md). 

- Un exemple de modèle, trouvé [ici](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md), utilise les zones et les domaines d’erreur pour une résilience maximale dans une région.

- Vous pouvez également déployer un hôte dédié à l’aide d’[Azure PowerShell](dedicated-hosts-powershell.md).