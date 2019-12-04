---
title: Configurer des adresses IP privées pour des machines virtuelles - Portail Azure
description: Découvrez comment configurer des adresses IP privées pour des machines virtuelles à l’aide du portail Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: tysonn
tags: azure-resource-manager
ms.assetid: 11245645-357d-4358-9a14-dd78e367b494
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: kumud
ms.openlocfilehash: bd734f171f4e10c4227fbab77485a788f02848b3
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196633"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal"></a>Configurer des adresses IP privées pour une machine virtuelle à l’aide du portail Azure

> [!div class="op_single_selector"]
> * [Portail Azure](virtual-networks-static-private-ip-arm-pportal.md)
> * [PowerShell](virtual-networks-static-private-ip-arm-ps.md)
> * [Interface de ligne de commande Azure](virtual-networks-static-private-ip-arm-cli.md)
> * [Portail Azure (classique)](virtual-networks-static-private-ip-classic-pportal.md)
> * [PowerShell (classique)](virtual-networks-static-private-ip-classic-ps.md)
> * [Interface de ligne de commande Azure (classique)](virtual-networks-static-private-ip-classic-cli.md)


[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Cet article traite du modèle de déploiement de Resource Manager. Vous pouvez également [gérer une adresse IP privée statique dans le modèle de déploiement classique](virtual-networks-static-private-ip-classic-pportal.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Les étapes de l’exemple suivant supposent qu’un environnement simple a déjà été créé. Si vous souhaitez exécuter les étapes telles qu’elles sont présentées dans ce document, commencez par créer l’environnement de test décrit dans [Créer un réseau virtuel](quick-create-portal.md).

## <a name="how-to-create-a-vm-for-testing-static-private-ip-addresses"></a>Création d’une machine virtuelle pour tester des adresses IP privées statiques
Vous ne pouvez pas définir une adresse IP privée statique lors de la création d'une machine virtuelle dans le mode de déploiement Resource Manager à l'aide du portail Azure. Vous devez d’abord créer la machine virtuelle, puis définir son adresse IP privée pour qu’elle soit statique.

Pour créer une machine virtuelle nommée *DNS01* dans le sous-réseau *FrontEnd* d’un réseau virtuel nommé *TestVNet*, suivez les étapes décrites ici.

1. Dans un navigateur, accédez à https://portal.azure.com et, si nécessaire, connectez-vous avec votre compte Azure.
2. Cliquez sur **Créer une ressource** > **Compute** > **Windows Server 2012 R2 Datacenter**, remarquez que la liste **Sélectionnez un modèle de déploiement** contient déjà **Resource Manager**, puis cliquez sur **Créer**, comme le montre la figure suivante.
   
    ![Création d'une machine virtuelle dans le portail Azure](./media/virtual-networks-static-ip-arm-pportal/figure01.png)
3. Dans le volet **Informations de base**, entrez le nom de la machine virtuelle à créer (*DNS01* dans le scénario), le compte d’administrateur local et un mot de passe, comme illustré dans la figure suivante.
   
    ![Volet Informations de base](./media/virtual-networks-static-ip-arm-pportal/figure02.png)
4. Assurez-vous que l’**Emplacement** sélectionné est *USA Centre*. Sous **Groupe de ressources**, cliquez sur **Sélectionner un groupe existant**, cliquez de nouveau sur **Groupe de ressources**, cliquez sur *TestRG*, puis sur **OK**.
   
    ![Volet Informations de base](./media/virtual-networks-static-ip-arm-pportal/figure03.png)
5. Dans le volet **Choisir une taille**, sélectionnez **A1 Standard**, puis cliquez sur **Sélectionner**.
   
    ![Volet Choisir une taille](./media/virtual-networks-static-ip-arm-pportal/figure04.png)    
6. Dans le volet **Paramètres**, vérifiez que les propriétés sont définies avec les valeurs suivantes, puis cliquez sur **OK**.
   
    -**Compte de stockage**: *vnetstorage*
   
   * **Réseau** : *TestVNet*
   * **Sous-réseau** : *FrontEnd*
     
     ![Volet Choisir une taille](./media/virtual-networks-static-ip-arm-pportal/figure05.png)     
7. Dans le volet **Résumé**, cliquez sur **OK**. Remarquez la vignette suivante affichée dans votre tableau de bord.
   
    ![Création d'une machine virtuelle dans le portail Azure](./media/virtual-networks-static-ip-arm-pportal/figure06.png)

Il est recommandé de ne pas assigner statiquement l’IP privée assignée à la machine virtuelle Azure au sein du système d’exploitation d’une machine virtuelle, sauf si nécessaire, par exemple lorsque [vous assignez plusieurs d’adresses IP à une machine virtuelle Windows](virtual-network-multiple-ip-addresses-portal.md). Si vous définissez manuellement l’adresse IP privée dans le système d’exploitation, assurez-vous qu’il s’agit de la même adresse que l’adresse IP privée assignée à [l’interface réseau](virtual-network-network-interface-addresses.md#change-ip-address-settings) Azure, ou vous pouvez perdre la connectivité à la machine virtuelle. En savoir plus sur les paramètres [d’adresse IP privée](virtual-network-network-interface-addresses.md#private). Vous ne devez jamais assigner manuellement l’adresse IP publique assignée à une machine virtuelle Azure au sein du système d’exploitation de la machine virtuelle.

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Récupération d’informations d’adresse IP privée statique pour une machine virtuelle
Pour afficher les informations d’adresse IP privée statique de la machine virtuelle créée lors des étapes ci-dessus, suivez les étapes décrites ici.

1. Dans le portail Azure, cliquez sur **PARCOURIR TOUT** > **Machines virtuelles** > **DNS01** > **Tous les paramètres** > **Interfaces réseau**, puis cliquez sur la seule interface réseau répertoriée.
   
    ![Déploiement d’une vignette de machine virtuelle](./media/virtual-networks-static-ip-arm-pportal/figure07.png)
2. Dans le volet **Interface réseau**, cliquez sur **Tous les paramètres** > **Adresses IP**, puis notez les valeurs des paramètres **Affectation** et **Adresse IP**.
   
    ![Déploiement d’une vignette de machine virtuelle](./media/virtual-networks-static-ip-arm-pportal/figure08.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Ajout d’une adresse IP privée statique à une machine virtuelle existante
Pour ajouter une adresse IP privée statique à la machine virtuelle créée lors des étapes ci-dessus, suivez les étapes décrites ici.

1. Dans le volet **Adresses IP** illustré ci-dessus, sous **Affectation**, cliquez sur **Statique**.
2. Tapez *192.168.1.101* dans **Adresse IP**, puis cliquez sur **Enregistrer**.
   
    ![Création d'une machine virtuelle dans le portail Azure](./media/virtual-networks-static-ip-arm-pportal/figure09.png)

> [!NOTE]
> Si, après avoir cliqué sur **Enregistrer**, vous remarquez que l’affectation est toujours définie sur **Dynamique**, cela signifie que l’adresse IP que vous avez tapée est déjà utilisée. Essayez une autre adresse IP.
> 
> 

Il est recommandé de ne pas assigner statiquement l’IP privée assignée à la machine virtuelle Azure au sein du système d’exploitation d’une machine virtuelle, sauf si nécessaire, par exemple lorsque [vous assignez plusieurs d’adresses IP à une machine virtuelle Windows](virtual-network-multiple-ip-addresses-portal.md). Si vous définissez manuellement l’adresse IP privée dans le système d’exploitation, assurez-vous qu’il s’agit de la même adresse que l’adresse IP privée assignée à [l’interface réseau](virtual-network-network-interface-addresses.md#change-ip-address-settings) Azure, ou vous pouvez perdre la connectivité à la machine virtuelle. En savoir plus sur les paramètres [d’adresse IP privée](virtual-network-network-interface-addresses.md#private). Vous ne devez jamais assigner manuellement l’adresse IP publique assignée à une machine virtuelle Azure au sein du système d’exploitation de la machine virtuelle.

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Comment supprimer une adresse IP privée statique d’une machine virtuelle
Pour supprimer l’adresse IP privée statique de la machine virtuelle créée ci-dessus, procédez comme suit :

Dans le volet **Adresses IP** illustré ci-dessus, sous **Affectation**, cliquez sur **Dynamique**, puis sur **Enregistrer**.

## <a name="set-ip-addresses-within-the-operating-system"></a>Définir des adresses IP au sein du système d’exploitation

Il est recommandé de ne pas assigner statiquement l’IP privée assignée à la machine virtuelle Azure au sein du système d’exploitation d’une machine virtuelle, sauf si nécessaire, par exemple lorsque [vous assignez plusieurs d’adresses IP à une machine virtuelle Windows](virtual-network-multiple-ip-addresses-portal.md). Si vous définissez manuellement l’adresse IP privée dans le système d’exploitation, assurez-vous qu’il s’agit de la même adresse que l’adresse IP privée assignée à [l’interface réseau](virtual-network-network-interface-addresses.md#change-ip-address-settings) Azure, ou vous pouvez perdre la connectivité à la machine virtuelle. En savoir plus sur les paramètres [d’adresse IP privée](virtual-network-network-interface-addresses.md#private). Vous ne devez jamais assigner manuellement l’adresse IP publique assignée à une machine virtuelle Azure au sein du système d’exploitation de la machine virtuelle.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur la gestion des [paramètres d’adresse IP](virtual-network-network-interface-addresses.md).

