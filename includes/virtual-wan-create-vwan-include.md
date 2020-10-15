---
title: Fichier include
description: Fichier include
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 07/09/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 5c9c8fa8099848eec09e689bf9491f71f378e9e3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90606168"
---
Dans un navigateur, accédez au Portail Azure et connectez-vous avec votre compte Azure.

1. Accédez à la page WAN Virtuel. Dans le portail, cliquez sur **+Créer une ressource**. Tapez **WAN virtuel** dans la zone de recherche et sélectionnez Entrée.
1. Sélectionnez **WAN virtuel** dans les résultats. Dans la page Virtual WAN, cliquez sur **Créer** pour ouvrir la page Créer un WAN.
1. Dans la page **Créer un WAN**, sous l’onglet **Fonctions de base**, renseignez les champs suivants :

   :::image type="content" source="./media/virtual-wan-create-vwan-include/basics.png" alt-text="La capture d’écran montre le volet Créer un WAN avec l’onglet Informations de base sélectionné.":::

   * **Abonnement** : sélectionnez l’abonnement que vous voulez utiliser.
   * **Groupe de ressources** : créez-en un ou utilisez-en un qui existe déjà.
   * **Emplacement du groupe de ressources** : choisissez un emplacement de la ressource dans la liste déroulante. Un WAN est une ressource globale et ne réside pas dans une région particulière. Toutefois, vous devez sélectionner une région pour gérer et localiser plus facilement la ressource WAN que vous créez.
   * **Nom** : tapez le nom que vous souhaitez donner à votre WAN.
   * **Type** : de base ou standard. Si vous créez un WAN de base, vous ne pourrez créer qu’un hub de base. Les hubs de base offrent uniquement une connectivité de site VPN à site.
1. Quand vous avez fini de renseigner les champs, cliquez sur **Vérifier + Créer**.
1. Après la validation, sélectionnez **Créer** pour créer le WAN virtuel.
