---
title: Fichier Include
description: Fichier Include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 04/04/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0dcb591db5f487a103feccf92ffa577a1cbf8b23
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2018
ms.locfileid: "30921425"
---
1. Accédez à la page de votre passerelle de réseau virtuel et ouvrez-la. Vous pouvez y accéder de plusieurs manières. Vous pouvez accéder à la passerelle VNet1GW en accédant à **TestVNet1 -> Vue d’ensemble -> Appareils connectés -> VNet1GW**.
2. Sur la page de VNet1GW, cliquez sur **Connexions**. En haut du panneau Connexions, cliquez sur **+ Ajouter** pour ouvrir la page **Ajouter une connexion**.

  ![Créer une connexion de site à site](./media/vpn-gateway-add-site-to-site-connection-portal-include/configure-site-to-site-connection.png)
3. Sur la page **Ajouter une connexion**, configurez les valeurs de votre connexion.

  - **Nom** : nommez votre connexion.
  - **Type de connexion** : sélectionnez **Site à site (IPSec)**.
  - **Passerelle de réseau virtuel** : la valeur est fixe, car vous vous connectez à partir de cette passerelle.
  - **Passerelle de réseau local** : cliquez sur **Choisir une passerelle de réseau local** et sélectionnez la passerelle de réseau local que vous souhaitez utiliser.
  - **Clé partagée** : la valeur doit correspondre à celle que vous utilisez pour votre périphérique VPN local. Dans cet exemple, nous avons utilisé « abc123 », mais vous pouvez (et devriez) utiliser une valeur plus complexe. L’important, c’est que la valeur que vous spécifiez ici doit être identique à celle spécifiée lors de la configuration de votre périphérique VPN.
  - Les autres valeurs pour **abonnement**, **groupe de ressources**, et **emplacement** sont fixes.

4. Cliquez sur **OK** pour créer votre connexion. Le message *Création de la connexion* clignote à l'écran.
5. Vous pouvez afficher la connexion dans la page **Connexions** de la passerelle de réseau virtuel. L’état passe de *Inconnu* à *Connexion*, puis à *Réussi*.