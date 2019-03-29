---
title: Créer une table de routage de hub virtuel WAN virtuel de Azure - portail Azure | Microsoft Docs
description: Table d’itinéraires de hub virtuel WAN virtuel de diriger le trafic vers une appliance virtuelle réseau à l’aide du portail.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to create a route table using the portal.
ms.openlocfilehash: 2c8b3b4671fd14f9b10b8491861ae2c652f0188b
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2019
ms.locfileid: "58580582"
---
# <a name="create-a-virtual-wan-hub-route-table-for-nvas-azure-portal"></a>Créer une table de routage hub WAN virtuel pour les appliances virtuelles réseau : Portail Azure

Cet article vous montre comment diriger le trafic à partir d’un hub pour une Appliance virtuelle réseau (NVA).

![Diagramme WAN virtuel](./media/virtual-wan-route-table/vwanroute.png)

## <a name="before-you-begin"></a>Avant de commencer

Vérifiez que vous respectez les critères suivants :

*  Vous avez une Appliance virtuelle réseau (NVA). Une Appliance virtuelle réseau est un logiciel tiers de votre choix est généralement mis en service à partir de la place de marché Azure dans un réseau virtuel.

    * Une adresse IP privée doit être attribuée à l’interface de réseau d’appliance virtuelle réseau.

    * L’appliance virtuelle réseau n’est pas déployé dans le hub virtuel. Elle doit être déployée dans un réseau virtuel distinct.

    *  Le réseau virtuel d’appliance virtuelle réseau peut avoir un ou plusieurs réseaux virtuels connectés à ce dernier. Dans cet article, nous faisons référence à l’appliance virtuelle réseau virtuel comme un « indirect réseau virtuel spoke ». Ces réseaux virtuels peuvent être connectés au réseau virtuel appliance virtuelle réseau à l’aide de l’homologation.
*  Vous avez créé des réseaux 2 virtuels. Ils sont utilisés en tant que réseaux virtuels spokes.

    * Dans cet exercice, les espaces d’adressage de réseau virtuel spoke sont : VNet1 : 10.0.2.0/24 et VNet2 : 10.0.3.0/24. Si vous avez besoin d’informations sur la façon de créer un réseau virtuel, consultez [créer un réseau virtuel](../virtual-network/quick-create-portal.md).

    * N’Assurez-vous qu’aucune passerelle de réseau virtuel dans un des réseaux virtuels.
    * Pour cette configuration, ces réseaux virtuels ne nécessitent pas d’un sous-réseau de passerelle.

## <a name="signin"></a>1. Se connecter

Dans un navigateur, accédez au [portail Azure](https://portal.azure.com) et connectez-vous avec votre compte Azure.

## <a name="vwan"></a>2. Créer un WAN virtuel

Créez un WAN virtuel. Dans le cadre de cet exercice, vous pouvez utiliser les valeurs suivantes :

* **Nom du WAN virtuel :** myVirtualWAN
* **Groupe de ressources :** testRG
* **Emplacement :** USA Ouest

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

## <a name="hub"></a>3. Créer un hub

Créer le hub. Dans le cadre de cet exercice, vous pouvez utiliser les valeurs suivantes :

* **Emplacement :** USA Ouest
* **Nom :** westushub
* **Espace d’adressage privé Hub :** 10.0.1.0/24

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="route"></a>4. Créer et appliquer une table de routage du hub

Mettre à jour le hub avec une table de routage du concentrateur. Dans le cadre de cet exercice, vous pouvez utiliser les valeurs suivantes :

* **Espaces d’adressage réseau virtuel spoke indirect :** 10.0.3.0/24 et 10.0.2.0/24 (VNet1 et VNet2)
* **Appliance virtuelle réseau DMZ privé IP adresse d’interface réseau :** 10.0.4.5

1. Accédez à votre réseau virtuel étendu.
2. Cliquez sur le hub pour lequel vous souhaitez créer une table de routage.
3. Cliquez sur le **...** , puis cliquez sur **hub virtuel modifier**.
4. Sur le **hub virtuel modifier** page, faites défiler vers le bas et sélectionnez la case à cocher **table d’utilisation pour le routage**.
5. Dans le **si le préfixe de destination est** colonne, ajouter les espaces d’adressage. Dans le **envoyer au tronçon suivant** colonne, ajouter l’appliance virtuelle réseau DMZ privé IP adresse d’interface réseau.
6. Cliquez sur **confirmer** pour mettre à jour de la ressource de hub avec les paramètres de table de routage.

## <a name="connections"></a>5. Créez les connexions de réseau virtuel

Créer une connexion à partir de chaque réseau virtuel (VNet1 et VNet2) de spoke indirecte au hub. Ensuite, créez une connexion entre le réseau virtuel d’appliance virtuelle réseau au concentrateur.

 Pour cette étape, vous pouvez utiliser les valeurs suivantes :

| Nom du réseau virtuel| Nom de connexion|
| --- | --- |
| VNet1 | testconnection1 |
| VNet2 | testconnection2 |
| NVAVNet | testconnection3 |

Répétez la procédure suivante pour chaque réseau virtuel dont vous souhaitez vous connecter.

1. Sur la page de votre WAN virtuel, cliquez sur **Connexion de réseau virtuel**.
2. Dans la page de connexion de réseau virtuel, cliquez sur **+ Ajouter une connexion**.
3. Dans la page **Ajouter une connexion**, renseignez les champs suivants :

    * **Nom de connexion** : nommez votre connexion.
    * **Hubs** : sélectionnez le hub que vous souhaitez associer à cette connexion.
    * **Abonnement** : vérifiez l’abonnement.
    * **Réseau virtuel** : sélectionnez le réseau virtuel que vous souhaitez connecter à ce hub. Le réseau virtuel ne peut pas avoir une passerelle de réseau virtuel déjà existante.
4. Cliquez sur **OK** pour créer la connexion.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le WAN virtuel, consultez la page [Vue d'ensemble de WAN virtuel](virtual-wan-about.md).