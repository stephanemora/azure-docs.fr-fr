---
title: Fichier Include
description: Fichier Include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/02/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8b585a47d3950d232eb3e8047c12ee8949030c95
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780202"
---
Pour créer un réseau virtuel dans le modèle de déploiement Resource Manager à l’aide du portail Azure, suivez les étapes ci-dessous. Utilisez les **valeurs d’exemple** si vous utilisez ces étapes sous forme de didacticiel. Si vous n’effectuez pas ces étapes sous forme d’un didacticiel, veillez à remplacer les valeurs par les vôtres. Pour plus d’informations sur l’utilisation des réseaux virtuels, voir [Présentation du réseau virtuel](../articles/virtual-network/virtual-networks-overview.md).

>[!NOTE]
>Afin que ce réseau virtuel puisse se connecter à un emplacement local, vous devez prendre contact avec votre administrateur de réseau local pour définir une plage d’adresses IP à utiliser spécifiquement pour ce réseau virtuel. Si une plage d’adresses en double existe des deux côtés de la connexion VPN, le trafic ne sera pas correctement acheminé. En outre, si vous souhaitez connecter ce réseau virtuel à un autre réseau virtuel, l’espace d’adressage ne peut pas se chevaucher avec un autre réseau virtuel. Veillez à planifier votre configuration réseau en conséquence.
>

1. Dans un navigateur, accédez au [portail Azure](https://portal.azure.com) et connectez-vous avec votre compte Azure.
2. Cliquez sur **Créer une ressource**. Dans le champ **Rechercher dans le marketplace**, tapez « réseau virtuel ». Localisez **Réseau virtuel** dans la liste renvoyée et cliquez sur cet élément pour ouvrir la page **Réseau virtuel**.
3. Cliquez sur **Créer**. La page **Créer un réseau virtuel** s’ouvre.
4. Sur la page **Créer un réseau virtuel**, configurez les paramètres du réseau virtuel. Lorsque vous renseignez les champs, le point d’exclamation rouge se transforme en coche verte si les caractères saisis dans le champ sont valides. Utilisez les valeurs suivantes :

   - **Nom** : VNet1
   - **Espace d’adressage** : 10.1.0.0/16
   - **Abonnement**: vérifiez que l’abonnement indiqué est celui que vous souhaitez utiliser. Vous pouvez modifier des abonnements à l’aide de la liste déroulante.
   - **Groupe de ressources** : TestRG1 (cliquez sur **Créer nouveau** pour créer un nouveau groupe)
   - **Emplacement** : USA Est
   - **Sous-réseau** : Serveur frontal
   - **Plage d’adresses** : 10.1.0.0/24

   ![Créer une page Réseau virtuel](./media/vpn-gateway-create-virtual-network-portal-include/create-virtual-network1.png)
5. Laissez DDoS sur Base, Points de terminaison de service sur Désactivés et Pare-feu sur Désactivé.
6. Pour créer le réseau virtuel, cliquez sur **Créer**.