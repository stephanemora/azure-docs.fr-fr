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
ms.openlocfilehash: 5149973fe63f867b49e55c970779c005e12536b9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "68780170"
---
1. Accédez à la page de votre passerelle de réseau virtuel. Vous pouvez y accéder de plusieurs manières. Vous pouvez accéder à la passerelle en accédant au **Nom de votre VNet -> Vue d’ensemble -> Appareils connectés -> Nom de votre passerelle**.
2. Sur la page de votre passerelle, cliquez sur **Connexions**. En haut du panneau Connexions, cliquez sur **+ Ajouter** pour ouvrir la page **Ajouter une connexion**.

   ![Créer une connexion de site à site](./media/vpn-gateway-add-site-to-site-connection-portal-include/configure-site-to-site-connection.png)
3. Sur la page **Ajouter une connexion**, configurez les valeurs de votre connexion.

   - **Nom** : nommez votre connexion.
   - **Type de connexion** : sélectionnez **Site à site (IPSec)** .
   - **Passerelle de réseau virtuel** : la valeur est fixe, car vous vous connectez à partir de cette passerelle.
   - **Passerelle de réseau local** : cliquez sur **Choisir une passerelle de réseau local** et sélectionnez la passerelle de réseau local que vous souhaitez utiliser.
   - **Clé partagée** : la valeur doit correspondre à celle que vous utilisez pour votre périphérique VPN local. Dans cet exemple, nous avons utilisé « abc123 », mais vous pouvez (et devriez) utiliser une valeur plus complexe. L’important, c’est que la valeur que vous spécifiez ici doit être identique à celle spécifiée lors de la configuration de votre périphérique VPN.
   - Les autres valeurs pour **abonnement**, **groupe de ressources**, et **emplacement** sont fixes.

4. Cliquez sur **OK** pour créer votre connexion. Le message *Création de la connexion* clignote à l'écran.
5. Vous pouvez afficher la connexion dans la page **Connexions** de la passerelle de réseau virtuel. L’état passe de *Inconnu* à *Connexion*, puis à *Réussi*.
