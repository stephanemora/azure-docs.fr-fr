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
ms.openlocfilehash: 19ad4e39ca4e402c37b2cfa69c7c306b6e5a2766
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60407673"
---
1. Accédez à la page de votre passerelle de réseau virtuel et ouvrez-la. Vous pouvez y accéder de plusieurs manières. Vous pouvez accéder à la passerelle VNet1GW en accédant à **TestVNet1 -> Vue d’ensemble -> Appareils connectés -> VNet1GW**.
2. Sur la page de VNet1GW, cliquez sur **Connexions**. En haut du panneau Connexions, cliquez sur **+ Ajouter** pour ouvrir la page **Ajouter une connexion**.

   ![Créer une connexion de site à site](./media/vpn-gateway-add-site-to-site-connection-portal-include/configure-site-to-site-connection.png)
3. Sur la page **Ajouter une connexion**, configurez les valeurs de votre connexion.

   - **Nom :** Nommez votre connexion.
   - **Type de connexion :** Sélectionnez **Site à site (IPsec)**.
   - **Passerelle de réseau virtuel :** La valeur est fixe, car vous vous connectez à partir de cette passerelle.
   - **Passerelle de réseau local :** Cliquez sur **Choisir une passerelle de réseau local**, puis sélectionnez la passerelle de réseau local à utiliser.
   - **Clé partagée** : la valeur doit correspondre à celle que vous utilisez pour votre périphérique VPN local. Dans cet exemple, nous avons utilisé « abc123 », mais vous pouvez (et devriez) utiliser une valeur plus complexe. L’important, c’est que la valeur que vous spécifiez ici doit être identique à celle spécifiée lors de la configuration de votre périphérique VPN.
   - Les autres valeurs pour **abonnement**, **groupe de ressources**, et **emplacement** sont fixes.

4. Cliquez sur **OK** pour créer votre connexion. Le message *Création de la connexion* clignote à l'écran.
5. Vous pouvez afficher la connexion dans la page **Connexions** de la passerelle de réseau virtuel. L’état passe de *Inconnu* à *Connexion*, puis à *Réussi*.
