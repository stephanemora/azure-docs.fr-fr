---
title: Créer une machine virtuelle avec une adresse IP publique statique - Portail Azure | Microsoft Docs
description: Découvrez comment créer une machine virtuelle avec une adresse IP publique statique à l’aide du portail Azure.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: allensu
ms.openlocfilehash: 0de28fc75d5eb1b0867e4ba6d8eda9f0f42c8498
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82148018"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-portal"></a>Créer une machine virtuelle avec une adresse IP publique statique à partir du portail Azure

Vous pouvez créer une machine virtuelle avec une adresse IP publique statique. Une adresse IP publique vous permet de communiquer avec une machine virtuelle à partir d’Internet. Attribuez une adresse IP publique statique, plutôt qu’une adresse dynamique, pour être certain que l’adresse ne changera jamais. Découvrez plus en détail les [adresses IP publiques statiques](virtual-network-ip-addresses-overview-arm.md#allocation-method). Pour changer le type de l’adresse IP publique attribuée à une machine virtuelle existante (de dynamique à statique), ou pour utiliser des adresses IP privées, consultez [Ajouter, modifier ou supprimer des adresses IP](virtual-network-network-interface-addresses.md). Les adresses IP publiques ont un [coût modique](https://azure.microsoft.com/pricing/details/ip-addresses) et le nombre d’adresses IP publiques que vous pouvez utiliser par abonnement est [limité](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur https://portal.azure.com.

## <a name="create-a-virtual-machine"></a>Création d'une machine virtuelle

1. Sélectionnez **+ Créer une ressource** en haut à gauche du portail Azure.
2. Sélectionnez **Calculer**, puis **Machine virtuelle Windows Server 2016** ou le système d’exploitation de votre choix.
3. Entrez ou sélectionnez les informations suivantes, acceptez les valeurs par défaut pour les autres paramètres, puis cliquez sur **OK** :

    |Paramètre|Valeur|
    |---|---|
    |Name|myVM|
    |Nom d'utilisateur| Entrez un nom d’utilisateur de votre choix.|
    |Mot de passe| Entrez un mot de passe de votre choix. Le mot de passe doit contenir au moins 12 caractères et satisfaire aux [exigences de complexité définies](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Subscription| Sélectionnez votre abonnement.|
    |Resource group| Sélectionnez **Utiliser l’existant**, puis **myResourceGroup**.|
    |Location| Sélectionnez **USA Est**.|

4. Choisissez une taille de machine virtuelle, puis cliquez sur **Sélectionner**.
5. Sous **Paramètres**, sélectionnez **Adresse IP publique**.
6. Entrez *myPublicIpAddress*, sélectionnez **Statique**, puis **OK**, comme illustré dans l’image suivante :

   ![Sélectionnez statique](./media/virtual-network-deploy-static-pip-arm-portal/select-static.png)

   Si l’adresse IP publique doit être une référence SKU standard, sélectionnez **Standard** sous **Référence (SKU)** . Découvrez plus en détail les [références SKU d’adresses IP publiques](virtual-network-ip-addresses-overview-arm.md#sku). Si la machine virtuelle doit être ajoutée au pool backend d’un équilibreur de charge Azure public, la référence SKU de l’adresse IP publique de la machine virtuelle doit correspondre à celle de l’équilibreur de charge. Pour plus d’informations, consultez [Azure Load Balancer](../load-balancer/concepts-limitations.md#skus).

6. Si nécessaire, sélectionnez un port sous **Sélectionner des ports d’entrée publics**. Le port 3389 est sélectionné pour autoriser l’accès à distance à la machine virtuelle Windows Server depuis Internet. Il est déconseillé d’ouvrir le port 3389 à partir d’Internet pour les charges de travail de production.

   ![Sélectionnez un port](./media/virtual-network-deploy-static-pip-arm-portal/select-port.png)

7. Acceptez les autres valeurs par défaut, puis sélectionnez **OK**.
8. Dans la page **Résumé**, sélectionnez **Créer**. Le déploiement de la machine virtuelle prend quelques minutes.
9. Une fois la machine virtuelle déployée, entrez *myPublicIpAddress* dans la zone de recherche située dans la partie supérieure du portail. Quand **myPublicIpAddress** apparaît dans les résultats de la recherche, sélectionnez cette entrée.
10. Vous pouvoir identifier l’adresse IP publique qui est attribuée et constater qu’elle est attribuée à la machine virtuelle **myVM**, comme illustré dans l’image suivante :

    ![Examinez l’adresse IP publique](./media/virtual-network-deploy-static-pip-arm-portal/public-ip-overview.png)

    Azure a affecté une adresse IP publique parmi les adresses utilisées dans la région dans laquelle vous avez créé la machine virtuelle. Vous pouvez télécharger la liste des plages (préfixes) pour les clouds Azure [Public](https://www.microsoft.com/download/details.aspx?id=56519), [Gouvernement américain](https://www.microsoft.com/download/details.aspx?id=57063), [Chine](https://www.microsoft.com/download/details.aspx?id=57062), et [Allemagne](https://www.microsoft.com/download/details.aspx?id=57064).

11. Sélectionnez **Configuration** pour vérifier que l’affectation est **Statique**.

    ![Examinez l’adresse IP publique](./media/virtual-network-deploy-static-pip-arm-portal/public-ip-configuration.png)

> [!WARNING]
> Ne modifiez pas les paramètres d’adresse IP dans le système d’exploitation de la machine virtuelle. Le système d’exploitation ne reconnaît pas les adresses IP publiques Azure. Même si vous avez la possibilité d’ajouter des paramètres d’adresse IP privée au système d’exploitation, nous vous déconseillons de le faire, à moins que cela soit nécessaire et pas avant d’avoir lu [Ajouter une adresse IP privée à un système d’exploitation](virtual-network-network-interface-addresses.md#private).

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous n’avez plus besoin du groupe de ressources, supprimez-le, ainsi que toutes les ressources qu’il contient :

1. Entrez *myResourceGroup* dans le champ **Recherche** en haut du portail. Quand **myResourceGroup** apparaît dans les résultats de la recherche, sélectionnez-le.
2. Sélectionnez **Supprimer le groupe de ressources**.
3. Entrez *myResourceGroup* dans **TAPER NOM DU GROUPE DE RESSOURCES :** puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [adresses IP publiques](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) dans Azure
- En savoir plus sur les [paramètres d’adresse IP publique](virtual-network-public-ip-address.md#create-a-public-ip-address)
- En savoir plus sur les [adresses IP privées](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) et l’affectation d’une [adresse IP privée statique](virtual-network-network-interface-addresses.md#add-ip-addresses) à une machine virtuelle Azure
- En savoir plus sur la création de machines virtuelles [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) et [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)