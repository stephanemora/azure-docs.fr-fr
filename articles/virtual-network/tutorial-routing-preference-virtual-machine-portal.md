---
title: Configurer la préférence de routage pour une machine virtuelle – Portail Azure
description: Apprenez à créer une machine virtuelle avec une adresse IP publique et choix d’une préférence de routage à l’aide du portail Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2021
ms.author: mnayak
ms.openlocfilehash: 0559d02ec603d12578fa46d9790d0711fde5e38b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101670909"
---
# <a name="configure-routing-preference-for-a-vm-using-the-azure-portal"></a>Configurer la préférence de routage pour une machine virtuelle à l’aide du portail Azure

Cet article explique comment configurer la préférence de routage pour une machine virtuelle. Le trafic lié à Internet à partir de la machine virtuelle est routé via le réseau du fournisseur de services Internet lorsque vous choisissez l’option de préférence de routage **Internet**. Le routage par défaut passe par le réseau Microsoft mondial.

Cet article explique comment créer une machine virtuelle avec une adresse IP publique définie pour acheminer le trafic via le réseau du fournisseur de services Internet à l’aide du portail Azure.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au [portail Azure](https://portal.azure.com/).

## <a name="create-a-virtual-machine"></a>Création d'une machine virtuelle

1. Sélectionnez **+ Créer une ressource** en haut à gauche du portail Azure.
2. Sélectionnez **Calculer**, puis **Machine virtuelle Windows Server 2016** ou le système d’exploitation de votre choix.
3. Entrez ou sélectionnez les informations suivantes, acceptez les valeurs par défaut pour les autres paramètres, puis cliquez sur **OK** :

    |Paramètre|Valeur|
    |---|---|
    |Nom|myVM|
    |Nom d'utilisateur| Entrez un nom d’utilisateur de votre choix.|
    |Mot de passe| Entrez un mot de passe de votre choix. Le mot de passe doit contenir au moins 12 caractères et satisfaire aux [exigences de complexité définies](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Abonnement| Sélectionnez votre abonnement.|
    |Resource group| Sélectionnez **Utiliser l’existant**, puis **myResourceGroup**.|
    |Emplacement| Sélectionnez **USA Est**.|

4. Choisissez une taille de machine virtuelle, puis cliquez sur **Sélectionner**.
5. Sous l’onglet **Mise en réseau**, cliquez sur **Créer nouveau** pour **Adresse IP publique**.
6. Entrez *myPublicIpAddress*, sélectionnez SKU comme **standard**, sélectionnez la préférence de routage **Internet**, puis appuyez sur **OK**, comme indiqué dans l’image suivante :

   ![Sélectionnez statique](./media/tutorial-routing-preference-virtual-machine-portal/routing-preference-internet-new.png)

6. Si nécessaire, sélectionnez un port sous **Sélectionner des ports d’entrée publics**. Le port 3389 est sélectionné pour autoriser l’accès à distance à la machine virtuelle Windows Server depuis Internet. Il est déconseillé d’ouvrir le port 3389 à partir d’Internet pour les charges de travail de production.

   ![Sélectionnez un port](./media/tutorial-routing-preference-virtual-machine-portal/pip-ports-new.png)

7. Acceptez les autres valeurs par défaut, puis sélectionnez **OK**.
8. Dans la page **Résumé**, sélectionnez **Créer**. Le déploiement de la machine virtuelle prend quelques minutes.
9. Une fois la machine virtuelle déployée, entrez *myPublicIpAddress* dans la zone de recherche située dans la partie supérieure du portail. Quand **myPublicIpAddress** apparaît dans les résultats de la recherche, sélectionnez cette entrée.
10. Vous pouvoir identifier l’adresse IP publique qui est attribuée et constater qu’elle est attribuée à la machine virtuelle **myVM**, comme illustré dans l’image suivante :

    ![La capture d’écran montre l’adresse IP publique de la carte réseau pour l’interface réseau mynic.](./media/tutorial-routing-preference-virtual-machine-portal/pip-properties-new.png)

11. Sélectionnez **Mise en réseau**, cliquez sur la carte réseau **mynic**, puis sélectionnez l’adresse IP publique pour confirmer que la préférence de routage est **Internet**.

    ![La capture d’écran montre l’adresse IP et la préférence de routage pour une adresse IP publique.](./media/tutorial-routing-preference-virtual-machine-portal/pip-routing-internet-new.png)

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous n’avez plus besoin du groupe de ressources, supprimez-le, ainsi que toutes les ressources qu’il contient :

1. Entrez *myResourceGroup* dans le champ **Recherche** en haut du portail. Quand **myResourceGroup** apparaît dans les résultats de la recherche, sélectionnez-le.
2. Sélectionnez **Supprimer le groupe de ressources**.
3. Entrez *myResourceGroup* dans **TAPER NOM DU GROUPE DE RESSOURCES :** puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur l’[adresse IP publique avec préférence de routage](routing-preference-overview.md).
- En savoir plus sur les [adresses IP publiques](./public-ip-addresses.md#public-ip-addresses) dans Azure.
- En savoir plus sur tous les [paramètres d’adresse IP publique](virtual-network-public-ip-address.md#create-a-public-ip-address).