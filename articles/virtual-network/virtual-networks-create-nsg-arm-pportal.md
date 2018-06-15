---
title: Créer un groupe de sécurité réseau - Portail Azure | Microsoft Docs
description: Découvrez comment créer et déployer des groupes de sécurité réseau à l’aide du portail Azure.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 5bc8fc2e-1e81-40e2-8231-0484cd5605cb
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d8a66de0b0239fef12168733eca7af85c8b08f82
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2018
ms.locfileid: "31793641"
---
# <a name="create-a-network-security-group-using-the-azure-portal"></a>Créer un groupe de sécurité réseau à l’aide du portail Azure

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Cet article traite du modèle de déploiement de Resource Manager. Vous pouvez également [créer des groupes de sécurité réseau dans le modèle de déploiement classique](virtual-networks-create-nsg-classic-ps.md).

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]


## <a name="create-the-nsg-frontend-nsg"></a>Créer le groupe de sécurité réseau NSG-FrontEnd
Pour créer le groupe de sécurité réseau **NSG-FrontEnd** comme dans le scénario, suivez les étapes suivantes :

1. Dans un navigateur, accédez à https://portal.azure.com et, si nécessaire, connectez-vous avec votre compte Azure.
2. Sélectionnez **+ Créer une ressource >** > **Groupes de sécurité réseau**.
   
    ![Portail Azure - Groupes de sécurité réseau](./media/virtual-networks-create-nsg-arm-pportal/figure11.png)
3. Sous **Groupes de sécurité réseau**, sélectionnez **Ajouter**.
   
    ![Portail Azure - Groupes de sécurité réseau](./media/virtual-networks-create-nsg-arm-pportal/figure12.png)
4. Sous **Créer un groupe de sécurité réseau**, créez un groupe de sécurité réseau nommé *NSG-FrontEnd* dans le groupe de ressources *RG-NSG*, puis sélectionnez **Créer**.
   
    ![Portail Azure - Groupes de sécurité réseau](./media/virtual-networks-create-nsg-arm-pportal/figure13.png)

## <a name="create-rules-in-an-existing-nsg"></a>Création de règles dans un NSG existant
Pour créer des règles dans un groupe de sécurité réseau existant à partir du portail Azure, suivez les étapes suivantes :

1. Sélectionnez **Tous les services**, puis recherchez **Groupes de sécurité réseau**. Lorsque **Groupes de sécurité réseau** apparaît, sélectionnez cette option.
2. Dans la liste des NSG, sélectionnez **NSG-FrontEnd** > **Règles de sécurité de trafic entrant**.
   
    ![Portail Azure - NSG-FrontEnd](./media/virtual-networks-create-nsg-arm-pportal/figure2.png)
3. Dans la liste des **règles de sécurité de trafic entrant**, cliquez sur **Ajouter**.
   
    ![Portail Azure - Ajouter une règle](./media/virtual-networks-create-nsg-arm-pportal/figure3.png)
4. Sous **Ajouter une règle de sécurité de trafic entrant**, créez une règle nommée *web-rule* avec une priorité de *200* autorisant l’accès via *TCP* sur le port *80* à une machine virtuelle de n’importe quelle source, puis sélectionnez **OK**. Notez que la plupart de ces paramètres sont déjà des valeurs par défaut.
   
    ![Portail Azure - Paramètres des règles](./media/virtual-networks-create-nsg-arm-pportal/figure4.png)
5. Après quelques secondes, vous voyez la nouvelle règle dans le NSG.
   
    ![Portail Azure - Nouvelle règle](./media/virtual-networks-create-nsg-arm-pportal/figure5.png)
6. Répétez les étapes 1 à 6 pour créer une règle de trafic entrant nommée *rdp-rule* avec la priorité *250* autorisant l’accès via *TCP* sur le port *3389* à une machine virtuelle de n’importe quelle source.

## <a name="associate-the-nsg-to-the-frontend-subnet"></a>Associer le groupe de sécurité réseau au sous-réseau FrontEnd

1. Sélectionnez **Tous les services >**, entrez **Groupes de ressources**, sélectionnez **Groupes de ressources** lorsque l’option s’affiche, puis sélectionnez **RG-NSG**.
2. Sous **RG-NSG**, sélectionnez **...** > **TestVNet**.
   
    ![Portail Azure - TestVNet](./media/virtual-networks-create-nsg-arm-pportal/figure14.png)
3. Sous **Paramètres**, sélectionnez **Sous-réseaux** > **FrontEnd** > **Groupe de sécurité réseau** > **NSG-FrontEnd**.
   
    ![Portail Azure - Paramètres de sous-réseau](./media/virtual-networks-create-nsg-arm-pportal/figure15.png)
4. Dans le panneau **FrontEnd**, sélectionnez **Enregistrer**.
   
    ![Portail Azure - Paramètres de sous-réseau](./media/virtual-networks-create-nsg-arm-pportal/figure16.png)

## <a name="create-the-nsg-backend-nsg"></a>Créer le groupe de sécurité réseau NSG-BackEnd
Pour créer le groupe de sécurité réseau **NSG-BackEnd** et l’associer au sous-réseau **BackEnd**, effectuez les étapes suivantes :

1. Pour créer un groupe de sécurité réseau nommé *NSG-BackEnd*, répétez les étapes de la section [Créer le groupe de sécurité réseau NSG-FrontEnd](#Create-the-NSG-FrontEnd-NSG).
2. Répétez les étapes de la section **Créer des règles dans un groupe de sécurité réseau existant** pour créer les règles [entrantes](#Create-rules-in-an-existing-NSG) dans le tableau ci-dessous.
   
   | Règle de trafic entrant | Règle de trafic sortant |
   | --- | --- |
   | ![Portail Azure - Règle entrante](./media/virtual-networks-create-nsg-arm-pportal/figure17.png) |![Portail Azure - Règle entrante](./media/virtual-networks-create-nsg-arm-pportal/figure18.png) |
3. Répétez les étapes de la section **Associer le groupe de sécurité réseau au sous-réseau FrontEnd** pour associer le groupe de sécurité réseau **NSG-Backend** au sous-réseau [BackEnd](#Associate-the-NSG-to-the-FrontEnd-subnet).

## <a name="next-steps"></a>Étapes suivantes
* Découvrez comment [gérer des groupes de sécurité réseau existants](manage-network-security-group.md)
* [Activez la journalisation](virtual-network-nsg-manage-log.md) des groupes de sécurité réseau.